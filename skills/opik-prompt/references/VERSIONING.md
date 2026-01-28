# Prompt Versioning Guide

Manage prompt versions for iterative improvement and A/B testing.

## How Versioning Works

- Each prompt has a unique **name**
- Creating a prompt with the same name creates a new **version**
- Versions are numbered sequentially: 1, 2, 3...
- You can retrieve any version by number, or get the latest

## Creating Versions

### Initial Prompt (Version 1)

```python
from opik import Opik

client = Opik()

prompt = client.create_prompt(
    name="product-description",
    prompt="Write a description for: {product}"
)
# prompt.version == 1
```

### New Version (Version 2)

```python
prompt = client.create_prompt(
    name="product-description",
    prompt="Write a compelling product description for: {product}\n\nHighlight key features."
)
# prompt.version == 2
```

## Retrieving Prompts

### Latest Version

```python
prompt = client.get_prompt(name="product-description")
# Returns the most recent version
```

### Specific Version

```python
prompt_v1 = client.get_prompt(name="product-description", version=1)
prompt_v2 = client.get_prompt(name="product-description", version=2)
```

## Comparing Prompts

### A/B Testing Workflow

```python
from opik.evaluation import evaluate_prompt
from opik.evaluation.metrics import AnswerRelevance

dataset = client.get_dataset("product-eval")

# Evaluate version 1
prompt_v1 = client.get_prompt(name="product-description", version=1)
result_v1 = evaluate_prompt(
    dataset=dataset,
    prompt_template=prompt_v1.prompt,
    model="gpt-4",
    scoring_metrics=[AnswerRelevance()]
)

# Evaluate version 2
prompt_v2 = client.get_prompt(name="product-description", version=2)
result_v2 = evaluate_prompt(
    dataset=dataset,
    prompt_template=prompt_v2.prompt,
    model="gpt-4",
    scoring_metrics=[AnswerRelevance()]
)

print(f"V1 score: {result_v1.average_score}")
print(f"V2 score: {result_v2.average_score}")
```

## Linking Prompts to Traces

Track which prompt version generated each response:

```python
import opik
from opik import Opik

client = Opik()
prompt = client.get_prompt(name="product-description")

@opik.track(
    name="generate_description",
    tags=[f"prompt_v{prompt.version}"]
)
def generate_description(product: str) -> str:
    formatted = prompt.format(product=product)
    # Replace with your LLM call
    response = openai_client.chat.completions.create(
        model="gpt-4",
        messages=[{"role": "user", "content": formatted}]
    )
    return response.choices[0].message.content
```

## Best Practices

1. **Use descriptive names:** `customer-support-v2-formal` not `prompt1`
2. **Document changes:** Keep notes on what changed between versions
3. **Test before deploying:** Use evaluation datasets to compare versions
4. **Use tags:** Link traces to prompt versions for analysis
5. **Roll back safely:** You can always retrieve older versions if needed
