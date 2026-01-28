---
name: opik-prompt
description: Manage prompt versions and run comparisons. Use when versioning prompts, comparing prompt variations, or optimizing prompt performance.
---

# opik-prompt

## Quick Reference

```
Create:   client.create_prompt(name="my-prompt", prompt="template")
Get:      client.get_prompt(name="my-prompt")
Version:  client.get_prompt(name="my-prompt", version=2)
```

> **Note:** Prompt management APIs are Python-primary in the Opik SDK.

## Quick Health Check

**Python:** Run `opik healthcheck`
**TypeScript:** Verify config exists at `~/.opik.config` or env vars are set

✅ "Connection successful" / config exists → Continue below
❌ "Connection failed" / no config → Run `/opik-setup` first, then return here

## Creating Prompts

```python
from opik import Opik

client = Opik()

# Create a new prompt
prompt = client.create_prompt(
    name="qa-assistant",
    prompt="Answer this question: {question}\n\nContext: {context}"
)
```

## Retrieving Prompts

### Latest Version

```python
prompt = client.get_prompt(name="qa-assistant")
print(prompt.prompt)  # The template string
```

### Specific Version

```python
prompt = client.get_prompt(name="qa-assistant", version=1)
```

## Using Prompts

### Format with Variables

```python
prompt = client.get_prompt(name="qa-assistant")

formatted = prompt.format(
    question="What is Python?",
    context="Python is a programming language."
)
# "Answer this question: What is Python?\n\nContext: Python is a programming language."
```

### With LLM Calls

```python
from openai import OpenAI

client = OpenAI()
opik = Opik()

prompt = opik.get_prompt(name="qa-assistant")
formatted = prompt.format(question=user_question, context=retrieved_context)

response = client.chat.completions.create(
    model="gpt-4",
    messages=[{"role": "user", "content": formatted}]
)
```

## Version Management

### Create New Version

Creating a prompt with the same name automatically creates a new version:

```python
# Version 1
client.create_prompt(name="qa-assistant", prompt="Answer: {question}")

# Version 2 (new version, same name)
client.create_prompt(name="qa-assistant", prompt="Please answer: {question}")
```

### List Versions

See [VERSIONING.md](references/VERSIONING.md) for detailed version management.

## Next Steps

- [VERSIONING.md](references/VERSIONING.md) - Detailed version management guide
- `/opik-eval` - Evaluate prompts against datasets
