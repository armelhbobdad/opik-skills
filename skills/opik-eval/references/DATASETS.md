# Dataset Management

Guide for creating and managing evaluation datasets in Opik.

## Creating Datasets

### Basic Creation

```python
from opik import Opik

client = Opik()
dataset = client.get_or_create_dataset("my-dataset")
```

This creates a new dataset or retrieves an existing one with the same name.

### With Description

```python
dataset = client.get_or_create_dataset(
    name="qa-evaluation",
    description="Q&A pairs for testing our assistant"
)
```

## Item Format

Dataset items are dictionaries with flexible schema:

```python
{
    "input": "The question or prompt",           # Required for most metrics
    "expected_output": "The expected response",  # Required for comparison metrics
    "metadata": {"source": "manual"}             # Optional metadata
}
```

### Common Schemas

**Q&A Evaluation:**
```python
{
    "input": "What is Python?",
    "expected_output": "A programming language"
}
```

**RAG Evaluation:**
```python
{
    "input": "What is the capital of France?",
    "expected_output": "Paris",
    "context": ["France is a country in Europe. Its capital is Paris."]
}
```

## Adding Items

### Single Item

```python
dataset.insert([{
    "input": "What is 2+2?",
    "expected_output": "4"
}])
```

### Bulk Insert

```python
items = [
    {"input": "Question 1", "expected_output": "Answer 1"},
    {"input": "Question 2", "expected_output": "Answer 2"},
    {"input": "Question 3", "expected_output": "Answer 3"},
]
dataset.insert(items)
```

## Loading from Files

### From CSV

```python
import csv

items = []
with open("data.csv") as f:
    reader = csv.DictReader(f)
    for row in reader:
        items.append({
            "input": row["question"],
            "expected_output": row["answer"]
        })

dataset.insert(items)
```

### From JSON

```python
import json

with open("data.json") as f:
    items = json.load(f)

dataset.insert(items)
```

### From JSONL

```python
import json

items = []
with open("data.jsonl") as f:
    for line in f:
        items.append(json.loads(line))

dataset.insert(items)
```

## Retrieving Datasets

### List All Datasets

```python
datasets = client.list_datasets()
for ds in datasets:
    print(f"{ds.name}: {ds.item_count} items")
```

### Get Specific Dataset

```python
dataset = client.get_dataset("my-dataset")
```

### Get Dataset Items

```python
items = dataset.get_items()
for item in items:
    print(item["input"])
```

## Best Practices

1. **Use descriptive names:** `qa-evaluation-v2` not `dataset1`
2. **Include metadata:** Track data sources, versions, creation dates
3. **Keep items focused:** Each item should test one specific case
4. **Version datasets:** Create new datasets for significant changes
5. **Large datasets:** For datasets with 1000+ items, insert in batches to avoid timeouts:
   ```python
   batch_size = 100
   for i in range(0, len(items), batch_size):
       dataset.insert(items[i:i+batch_size])
   ```
