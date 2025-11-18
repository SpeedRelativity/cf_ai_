# CLOUDFLARE INTERNSHIP PROJECT

Documenting everything I've done here and how it works.

## Project Overview

When I ran the starter template code, 3 of the 4 conditions were pre-filled.

```bash
npm create cloudflare@latest agents-starter -- --template=cloudflare/agents-starter
npx wrangler@latest deploy
```

1. Workflow / coordination (recommend using Workflows, Workers or Durable Objects)
2. User input via chat or voice (recommend using Pages or Realtime)
3. Memory or state

The only thing we needed to work on was the LLM integration (LLaMa 3.3 via Workers AI).

So here's what I did:

## Step 1: Update Backend to Use Workers AI

### Changed the AI Provider (src/server.ts)

```typescript
// Replaced this:
import { openai } from "@ai-sdk/openai";

// With this:
import { createWorkersAI } from "workers-ai-provider";
```

### Initialize Model Inside Agent Method

```typescript
async onChatMessage(onFinish, _options) {
  // Create Workers AI provider using the AI binding
  const workersai = createWorkersAI({ binding: this.env.AI });
  const model = workersai("@cf/meta/llama-3.3-70b-instruct-fp8-fast" as any);

  // Rest of the code stays the same - just using a different model!
}
```

### Update Health Check Endpoint

Removed old OpenAI API key checks and updated to reflect Workers AI:

```typescript
if (url.pathname === "/check-open-ai-key") {
  return Response.json({
    success: true,
    provider: "Workers AI",
    model: "llama-3.3-70b-instruct-fp8-fast"
  });
}
```

## Step 2: Update Frontend Error Messages (src/app.tsx)

Changed error messages from "OpenAI API Key Not Configured" to "AI Service Not Available" to reflect the new Workers AI setup.

```typescript
const hasAIReadyPromise = fetch("/check-open-ai-key").then((res) =>
  res.json<{ success: boolean; provider?: string; model?: string }>()
);
```

## Step 3: Configuration Files

The AI binding was already configured in `wrangler.jsonc` - no changes needed:

```jsonc
"ai": {
  "binding": "AI",
  "remote": true
}
```

Durable Objects configuration for state management:

```jsonc
"durable_objects": {
  "bindings": [
    {
      "name": "Chat",
      "class_name": "Chat"
    }
  ]
}
```

## How It Works

### The Architecture

```
User Browser (React UI)
    ↓
Cloudflare Worker (src/server.ts)
    ↓
Durable Object (Chat Agent)
    ↓
Workers AI (Llama 3.3)
```

The basic Workers AI pattern is:

```typescript
env.AI.run(modelName, { prompt: "..." });
```

But we wrap it with `createWorkersAI()` to get the advanced features:

```typescript
const workersai = createWorkersAI({ binding: env.AI });
const model = workersai(modelName);
// Now works with streamText(), tools, etc.
```

## Running the Project

1. Clone the repository
2. Install dependencies: `npm install`
3. Run development server: `npm run dev`
4. Access the application at `http://localhost:5173`
5. Deploy to production: `npm run deploy`

## Resources Used

1. **Cloudflare Agents API**: https://developers.cloudflare.com/agents/api-reference/agents-api/
2. **Llama 3.3 Model Docs**: https://developers.cloudflare.com/workers-ai/models/llama-3.3-70b-instruct-fp8-fast/
3. **Agents Starter Template**: https://github.com/cloudflare/agents-starter
4. **Workers AI Provider**: https://github.com/cloudflare/workers-ai-provider
5. **Workers AI Docs**: https://developers.cloudflare.com/workers-ai/

Built for Cloudflare internship application - demonstrating Workers AI, Durable Objects, and modern AI application architecture.
