# Opik Skills

Agent Skills that bring [Opik's](https://www.comet.com/docs/opik) LLM observability stack directly into your AI coding assistant—trace, evaluate, and iterate without leaving your editor.

## Quick Install

```bash
npx skills add armelhbobdad/opik-skills
```

Install to specific agents:

```bash
npx skills add armelhbobdad/opik-skills -a claude-code -a cursor -a gemini-cli -a codex
```

## Skills

| Skill | Description | Use When |
|-------|-------------|----------|
| `opik-setup` | Configure Opik SDK | Setting up a new project, connecting to Opik Cloud or self-hosted |
| `opik-trace` | Add LLM tracing | Instrumenting functions, integrating frameworks (LangChain, OpenAI, etc.) |
| `opik-eval` | Create and run evaluations | Testing prompts, measuring quality, comparing models |
| `opik-prompt` | Manage prompt versions | Versioning prompts, comparing variations, optimizing performance |

## Supported Agents

**Full Support (Big 4):**
- Claude Code
- Cursor
- Gemini CLI
- Codex

**Beta Support:**
- Antigravity
- OpenCode

## Language Support

Both Python and TypeScript are supported:

**Python:**
```bash
pip install opik
opik configure
```

**TypeScript:**
```bash
npm install opik
npx opik-ts configure
```

> **Note:** Setup and tracing have full Python/TypeScript parity. Evaluation and prompt management APIs are Python-primary in the Opik SDK.

## Links

- [Opik Documentation](https://www.comet.com/docs/opik)
- [Agent Skills Specification](https://agentskills.io)
- [Opik GitHub](https://github.com/comet-ml/opik)

## License

Apache-2.0
