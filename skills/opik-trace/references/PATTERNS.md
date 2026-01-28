# Integration Patterns Deep Dive

Detailed code examples for each Opik integration pattern.

## client-wrapper

Wrap SDK clients to automatically trace all API calls. Works with OpenAI and Anthropic.

### When to Use

- You're using OpenAI or Anthropic SDKs directly
- You want automatic tracing with zero code changes to your API calls
- You need to capture token usage, latency, and responses

### Python - OpenAI

```python
from opik.integrations.openai import track_openai
from openai import OpenAI

# Wrap the client
client = track_openai(OpenAI())

# Use normally - all calls are traced
response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"}
    ]
)
```

### Python - Anthropic

```python
from opik.integrations.anthropic import track_anthropic
import anthropic

# Wrap the client
client = track_anthropic(anthropic.Anthropic())

# Use normally
message = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello!"}]
)
```

### TypeScript - OpenAI

**Required packages:** `npm install opik opik-openai openai`

```typescript
import { trackOpenAI } from "opik-openai";
import OpenAI from "openai";

// Wrap the client
const client = trackOpenAI(new OpenAI());

// Use normally - all calls are traced
const response = await client.chat.completions.create({
  model: "gpt-4",
  messages: [
    { role: "system", content: "You are a helpful assistant." },
    { role: "user", content: "Hello!" }
  ]
});
```

### Common Options

```python
# Python
client = track_openai(
    OpenAI(),
    project_name="my-project",  # Override default project
    tags=["production", "v2"]   # Add tags to all traces
)
```

```typescript
// TypeScript
const client = trackOpenAI(new OpenAI(), {
  projectName: "my-project",
  tags: ["production", "v2"]
});
```

---

## callback-handler

Pass an Opik callback handler to framework-specific callback systems.

### When to Use

- You're using LangChain
- You want to trace chain executions, tool calls, and LLM interactions
- You need visibility into multi-step agent workflows

### Python - LangChain

**Required packages:** `pip install opik langchain langchain-openai`

```python
from opik.integrations.langchain import OpikTracer
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate

# Create tracer
tracer = OpikTracer(project_name="langchain-app")

# Create chain
prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant."),
    ("user", "{input}")
])
llm = ChatOpenAI(model="gpt-4")
chain = prompt | llm

# Pass tracer in config
response = chain.invoke(
    {"input": "Hello!"},
    config={"callbacks": [tracer]}
)
```

### With LangChain Agents

```python
from langchain.agents import create_react_agent, AgentExecutor
from opik.integrations.langchain import OpikTracer

tracer = OpikTracer()

# Agent execution with tracing
agent_executor = AgentExecutor(agent=agent, tools=tools)
result = agent_executor.invoke(
    {"input": "What's the weather?"},
    config={"callbacks": [tracer]}
)
```

### Tracer Options

```python
tracer = OpikTracer(
    project_name="my-project",
    tags=["agent", "production"],
    metadata={"version": "1.0"}
)
```

---

## global-handler

Set a global handler to automatically trace all operations in a framework.

### When to Use

- You're using LlamaIndex
- You want automatic tracing without modifying each call
- You have complex RAG pipelines with many components

### Python - LlamaIndex

**Required packages:** `pip install opik llama-index`

```python
from llama_index.core import set_global_handler, VectorStoreIndex
from llama_index.core import SimpleDirectoryReader

# Set once at startup
set_global_handler("opik", project_name="llamaindex-app")

# All operations are now traced automatically
documents = SimpleDirectoryReader("data").load_data()
index = VectorStoreIndex.from_documents(documents)
query_engine = index.as_query_engine()

# This query and all sub-operations are traced
response = query_engine.query("What is the document about?")
```

### With Custom Settings

```python
import opik
from llama_index.core import set_global_handler

# Configure Opik programmatically if not using CLI/env vars
opik.configure(project_name="my-rag-app")

# Then set global handler
set_global_handler("opik")
```

---

## decorator

Decorate functions to trace their execution, inputs, outputs, and errors.

### When to Use

- You have custom functions that aren't covered by framework integrations
- You want fine-grained control over what gets traced
- You're building custom LLM pipelines

### Python

```python
import opik

@opik.track(name="process_query")
def process_query(query: str) -> str:
    # All inputs and outputs are captured
    result = do_something(query)
    return result

@opik.track(name="llm_call", type="llm")
def call_llm(prompt: str, model: str = "gpt-4") -> str:
    # Mark as LLM type for special handling
    response = openai_client.chat.completions.create(
        model=model,
        messages=[{"role": "user", "content": prompt}]
    )
    return response.choices[0].message.content
```

### TypeScript

```typescript
import { track } from "opik";

const processQuery = track(async (query: string): Promise<string> => {
  const result = await doSomething(query);
  return result;
}, { name: "process_query" });

// Usage
const result = await processQuery("Hello");
```

### Nested Traces

```python
@opik.track(name="main_workflow")
def main_workflow(input: str):
    # Child spans are automatically nested
    step1_result = step_one(input)
    step2_result = step_two(step1_result)
    return step2_result

@opik.track(name="step_one")
def step_one(data: str):
    return process(data)

@opik.track(name="step_two")
def step_two(data: str):
    return finalize(data)
```

### Decorator Options

```python
@opik.track(
    name="my_function",        # Custom name (default: function name)
    type="llm",                # Span type: "llm", "tool", "general"
    project_name="my-project", # Override default project
    tags=["important"],        # Tags for filtering
    capture_input=True,        # Capture function inputs
    capture_output=True        # Capture function outputs
)
def my_function():
    pass
```

---

## context-manager

Manually create and manage spans for complex control flow.

### When to Use

- You need manual control over span boundaries
- Decorators don't fit your code structure
- You're tracing async operations with complex lifecycles

### Python

```python
import opik

# Create a trace manually
with opik.start_as_current_trace(name="my_workflow") as trace:
    # Add spans within the trace
    with trace.span(name="step_1") as span:
        result = do_step_1()
        span.set_attribute("result_size", len(result))

    with trace.span(name="step_2") as span:
        final = do_step_2(result)
        span.set_attribute("status", "success")

# Or just create a span in the current trace context
with opik.start_span(name="custom_operation") as span:
    result = do_something()
    span.set_attribute("custom_key", "custom_value")
```

### Setting Attributes

```python
with opik.start_span(name="llm_call") as span:
    response = call_llm(prompt)

    # Add metadata to the span
    span.set_attribute("model", "gpt-4")
    span.set_attribute("tokens", 150)
    span.set_attribute("latency_ms", 234)
```

### Error Handling

```python
with opik.start_span(name="risky_operation") as span:
    try:
        result = risky_function()
        span.set_attribute("status", "success")
    except Exception as e:
        span.set_attribute("status", "error")
        span.set_attribute("error_message", str(e))
        raise
```
