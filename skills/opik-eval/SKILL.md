---
name: opik-eval
description: Create and run evaluations on your LLM outputs. Use when testing prompts, measuring quality, comparing models, or creating evaluation datasets.
---

# opik-eval

## Quick Reference

```
Dataset:    client.get_or_create_dataset("my-dataset")
Evaluate:   evaluate(dataset, task_fn, metrics)
Metrics:    Hallucination, AnswerRelevance, ContextPrecision, ContextRecall, Equals
```

> **Note:** Evaluation APIs are Python-primary in the Opik SDK. TypeScript support is limited for evaluation workflows.

## Quick Health Check

**Python:** Run `opik healthcheck`
**TypeScript:** Verify config exists at `~/.opik.config` or env vars are set

✅ "Connection successful" / config exists → Continue below
❌ "Connection failed" / no config → Run `/opik-setup` first, then return here

Note: `OPIK_PROJECT_NAME` is optional - evaluations go to "default" project if unset.

## Basic Evaluation Workflow

### 1. Create a Dataset

```python
from opik import Opik

client = Opik()
dataset = client.get_or_create_dataset("my-eval-dataset")

# Add items
dataset.insert([
    {"input": "What is Python?", "expected_output": "A programming language"},
    {"input": "What is JavaScript?", "expected_output": "A programming language"},
])
```

### 2. Define Your Task

```python
from openai import OpenAI

openai_client = OpenAI()

def my_llm_task(item):
    # Your LLM call here
    response = openai_client.chat.completions.create(
        model="gpt-4",
        messages=[{"role": "user", "content": item["input"]}]
    )
    return {
        "output": response.choices[0].message.content,
        "context": ["Retrieved context here"]  # Optional, for context-based metrics
    }
```

### 3. Run Evaluation

```python
from opik.evaluation import evaluate
from opik.evaluation.metrics import Equals, AnswerRelevance

result = evaluate(
    dataset=dataset,
    task=my_llm_task,
    scoring_metrics=[Equals(), AnswerRelevance()]
)

print(f"Average score: {result.average_score}")
```

## Available Metrics

| Metric | Description | Use When |
|--------|-------------|----------|
| `Hallucination` | Detects factual errors | Checking output accuracy |
| `AnswerRelevance` | Measures response relevance | Q&A systems |
| `ContextPrecision` | Context retrieval quality | RAG applications |
| `ContextRecall` | Context coverage | RAG applications |
| `Equals` | Exact match comparison | Deterministic outputs |

See [METRICS.md](references/METRICS.md) for detailed metric documentation.

## Prompt Evaluation

Evaluate different prompts against the same dataset:

```python
from opik.evaluation import evaluate_prompt

result = evaluate_prompt(
    dataset=dataset,
    prompt_template="Answer this question: {input}",
    model="gpt-4",
    scoring_metrics=[AnswerRelevance()]
)
```

## Next Steps

- [METRICS.md](references/METRICS.md) - Detailed metric documentation
- [DATASETS.md](references/DATASETS.md) - Dataset management guide

## Common Patterns

### Running Multiple Metrics

```python
from opik.evaluation.metrics import (
    Hallucination,
    AnswerRelevance,
    ContextPrecision
)

result = evaluate(
    dataset=dataset,
    task=my_task,
    scoring_metrics=[
        Hallucination(),
        AnswerRelevance(),
        ContextPrecision()
    ]
)
```

### Comparing Models

```python
# Evaluate model A
result_a = evaluate(dataset=dataset, task=model_a_task, scoring_metrics=metrics)

# Evaluate model B
result_b = evaluate(dataset=dataset, task=model_b_task, scoring_metrics=metrics)

# Compare results in Opik dashboard
```
