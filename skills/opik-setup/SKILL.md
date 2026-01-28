---
name: opik-setup
description: Configure Opik SDK for LLM observability. Use when setting up a new project, connecting to Opik Cloud, or configuring self-hosted Opik.
---

# opik-setup

## Quick Reference

```
Python:    pip install opik && opik configure
TypeScript: npm install opik && npx opik-ts configure
Verify:     opik healthcheck (Python only)
Config:    ~/.opik.config or OPIK_API_KEY env var
```

## Python Setup

### 1. Install

```bash
pip install opik
```

### 2. Configure

**Interactive (recommended):**
```bash
opik configure
```

**Or set environment variables:**
```bash
export OPIK_API_KEY="your-api-key"
export OPIK_WORKSPACE="your-workspace"  # Optional
export OPIK_PROJECT_NAME="my-project"   # Optional, defaults to "default"
```

### 3. Verify

```bash
opik healthcheck
```

✅ "Connection successful" → Ready to trace
❌ "Connection failed" → Check API key and network

## TypeScript Setup

### 1. Install

```bash
npm install opik
```

### 2. Configure

**Interactive:**
```bash
npx opik-ts configure
```

**Or set environment variables:**
```bash
export OPIK_API_KEY="your-api-key"
export OPIK_WORKSPACE="your-workspace"  # Optional
export OPIK_PROJECT_NAME="my-project"   # Optional
```

### 3. Verify

> **Note:** TypeScript SDK doesn't have a `healthcheck` CLI command. Use these alternatives:

Check config file exists:
```bash
cat ~/.opik.config
```

Or verify env var is set:
```bash
echo $OPIK_API_KEY
```

Or test with a simple script:
```typescript
import { Opik } from "opik";
const client = new Opik();
console.log("Opik configured successfully");
```

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `OPIK_API_KEY` | Yes* | API key from comet.com (Cloud) |
| `OPIK_URL_OVERRIDE` | Yes* | Custom URL (self-hosted only) |
| `OPIK_WORKSPACE` | No | Workspace name |
| `OPIK_PROJECT_NAME` | No | Project name (defaults to "default") |

*Either `OPIK_API_KEY` (Cloud) or `OPIK_URL_OVERRIDE` (self-hosted) is required.

## Self-Hosted Setup

For self-hosted Opik instances:

```bash
export OPIK_URL_OVERRIDE="http://your-opik-server:5173/api"
```

No API key required for self-hosted deployments.

## Next Steps

After setup is complete:
- Add tracing: `/opik-trace`
- Run evaluations: `/opik-eval`
- Manage prompts: `/opik-prompt`

## Troubleshooting

See [CONFIGURATION.md](references/CONFIGURATION.md) for:
- Configuration precedence order
- Config file format details
- Common error solutions
