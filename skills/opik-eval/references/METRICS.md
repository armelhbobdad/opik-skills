# Evaluation Metrics Reference

Built-in metrics for evaluating LLM outputs with Opik.

## Built-in Metrics

### Hallucination

Detects when the output contains information not supported by the context.

```python
from opik.evaluation.metrics import Hallucination

metric = Hallucination()

# Requires context in task output
def task(item):
    return {
        "output": "Paris is the capital of France",
        "context": ["France is a country in Europe. Its capital is Paris."]
    }
```

**When to use:** Fact-checking, RAG applications, knowledge-grounded responses.

### AnswerRelevance

Measures how relevant the answer is to the input question.

```python
from opik.evaluation.metrics import AnswerRelevance

metric = AnswerRelevance()

# Compares output relevance to input
def task(item):
    return {
        "output": "Python is a programming language",
        "input": item["input"]  # "What is Python?"
    }
```

**When to use:** Q&A systems, chatbots, search applications.

### ContextPrecision

Evaluates how much of the retrieved context is actually relevant.

```python
from opik.evaluation.metrics import ContextPrecision

metric = ContextPrecision()

# Evaluates context quality
def task(item):
    return {
        "output": "The answer",
        "context": ["Relevant chunk", "Irrelevant chunk"]
    }
```

**When to use:** RAG retrieval optimization, context window efficiency.

### ContextRecall

Measures if all necessary information is present in the context.

```python
from opik.evaluation.metrics import ContextRecall

metric = ContextRecall()

# Checks if context covers the expected answer
def task(item):
    return {
        "output": "The answer",
        "context": ["Context chunks..."],
        "expected_output": item["expected_output"]
    }
```

**When to use:** RAG completeness, knowledge base coverage.

### Equals

Simple exact match comparison between output and expected output.

```python
from opik.evaluation.metrics import Equals

metric = Equals()

# Exact match check
def task(item):
    return {
        "output": "42",
        "expected_output": item["expected_output"]  # "42"
    }
```

**When to use:** Deterministic outputs, classification tasks, structured outputs.

## Custom Metrics

Create custom metrics for domain-specific evaluation:

```python
from opik.evaluation.metrics import BaseMetric, MetricResult

class CustomMetric(BaseMetric):
    name = "custom_metric"

    def score(self, output: str, expected_output: str = None, **kwargs) -> MetricResult:
        # Your scoring logic
        score = calculate_score(output, expected_output)

        return MetricResult(
            name=self.name,
            score=score,
            reason="Explanation of score"
        )
```

## Combining Metrics

Use multiple metrics in a single evaluation:

```python
from opik.evaluation import evaluate
from opik.evaluation.metrics import (
    Hallucination,
    AnswerRelevance,
    ContextPrecision,
    ContextRecall,
    Equals
)

result = evaluate(
    dataset=dataset,
    task=my_task,
    scoring_metrics=[
        Hallucination(),
        AnswerRelevance(),
        ContextPrecision(),
        ContextRecall(),
        Equals()
    ]
)

# Access individual metric scores
for metric_name, scores in result.scores.items():
    print(f"{metric_name}: {sum(scores)/len(scores):.2f}")
```

## Metric Requirements

| Metric | Required Fields |
|--------|-----------------|
| Hallucination | `output`, `context` |
| AnswerRelevance | `output`, `input` |
| ContextPrecision | `output`, `context` |
| ContextRecall | `output`, `context`, `expected_output` |
| Equals | `output`, `expected_output` |
