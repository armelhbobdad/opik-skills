# Framework Integration Router

Quick lookup for Opik integration patterns. Each entry links to detailed examples in [PATTERNS.md](PATTERNS.md).

## Framework Lookup

| Framework | Pattern | Link |
|-----------|---------|------|
| OpenAI | Client Wrapper | [PATTERNS.md#client-wrapper](PATTERNS.md#client-wrapper) |
| Anthropic | Client Wrapper | [PATTERNS.md#client-wrapper](PATTERNS.md#client-wrapper) |
| LangChain | Callback Handler | [PATTERNS.md#callback-handler](PATTERNS.md#callback-handler) |
| LlamaIndex | Global Handler | [PATTERNS.md#global-handler](PATTERNS.md#global-handler) |
| Custom Functions | Decorator | [PATTERNS.md#decorator](PATTERNS.md#decorator) |
| Manual Spans | Context Manager | [PATTERNS.md#context-manager](PATTERNS.md#context-manager) |

## How to Use

1. Find your framework in the table above
2. Click the link to see the detailed pattern
3. Copy the code example for your language (Python/TypeScript)

## Quick Detection

Check your dependencies:

**Python:**
```bash
grep -E "openai|anthropic|langchain|llama" requirements.txt pyproject.toml 2>/dev/null
```

**Node/TypeScript:**
```bash
grep -E "openai|anthropic|langchain|llamaindex" package.json
```

## Pattern Summary

- **Client Wrapper**: Wrap SDK clients for automatic tracing (OpenAI, Anthropic)
- **Callback Handler**: Pass tracer to callbacks (LangChain)
- **Global Handler**: Set once, trace everything (LlamaIndex)
- **Decorator**: Annotate functions for tracing (any Python/TS code)
- **Context Manager**: Manual span creation for complex flows

## Language Support

| Pattern | Python | TypeScript |
|---------|--------|------------|
| Client Wrapper | ✅ | ✅ (via opik-openai) |
| Callback Handler | ✅ | ❌ |
| Global Handler | ✅ | ❌ |
| Decorator | ✅ | ✅ |
| Context Manager | ✅ | ❌ |
