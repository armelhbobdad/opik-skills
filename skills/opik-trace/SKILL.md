---
name: opik-trace
description: Add LLM tracing and observability to your code. Use when instrumenting functions, integrating frameworks (LangChain, OpenAI, etc.), or adding custom spans.
---

# opik-trace

## Quick Reference

```
Decorator:  @opik.track(name="my_func")
OpenAI:     client = track_openai(OpenAI())
LangChain:  config={"callbacks": [OpikTracer()]}
LlamaIndex: set_global_handler("opik")
```

## Quick Health Check

**Python:** Run `opik healthcheck`
**TypeScript:** Verify config exists at `~/.opik.config` or env vars are set

✅ "Connection successful" / config exists → Continue below
❌ "Connection failed" / no config → Run `/opik-setup` first, then return here

Note: `OPIK_PROJECT_NAME` is optional - traces go to "default" project if unset.

## Framework Detection

Check your project dependencies to find the right integration pattern:

**Python:** Look in `requirements.txt` or `pyproject.toml`
**TypeScript/Node:** Look in `package.json`

| If you see... | Use this pattern |
|---------------|------------------|
| `openai` | [Client Wrapper](#client-wrapper) |
| `anthropic` | [Client Wrapper](#client-wrapper) |
| `langchain` | [Callback Handler](#callback-handler) |
| `llama-index` or `llama_index` | [Global Handler](#global-handler) |
| None of the above | [Decorator Pattern](#decorator-pattern) |

## Integration Patterns

### Client Wrapper

For OpenAI and Anthropic SDKs.

**Python:**
```python
from opik.integrations.openai import track_openai
from openai import OpenAI

client = track_openai(OpenAI())
response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "Hello"}]
)
# Automatically traced!
```

**TypeScript:**
```typescript
import { trackOpenAI } from "opik-openai";
import OpenAI from "openai";

const client = trackOpenAI(new OpenAI());
const response = await client.chat.completions.create({
  model: "gpt-4",
  messages: [{ role: "user", content: "Hello" }]
});
// Automatically traced!
```

> Note: TypeScript requires `npm install opik opik-openai`

### Callback Handler

For LangChain.

**Python:**
```python
from opik.integrations.langchain import OpikTracer

tracer = OpikTracer()
chain.invoke(input, config={"callbacks": [tracer]})
```

### Global Handler

For LlamaIndex.

**Python:**
```python
from llama_index.core import set_global_handler

set_global_handler("opik")
# All LlamaIndex operations now traced
```

### Decorator Pattern

For custom functions.

**Python:**
```python
import opik

@opik.track(name="process_query")
def process_query(query: str) -> str:
    result = f"Processed: {query}"  # Your logic here
    return result
```

**TypeScript:**
```typescript
import { track } from "opik";

const processQuery = track(async (query: string) => {
  const result = `Processed: ${query}`;  // Your logic here
  return result;
}, { name: "process_query" });
```

## Next Steps

See [FRAMEWORKS.md](references/FRAMEWORKS.md) for detailed integration patterns for each framework.

## Common Options

| Option | Description |
|--------|-------------|
| `name` | Custom name for the span |
| `project_name` | Override default project |
| `tags` | List of tags for filtering |
| `metadata` | Additional key-value data |
