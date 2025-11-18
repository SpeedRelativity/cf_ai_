## Prompt 1:

I'm applying for cloudflare summer internship and there's an optional app we must build. I want you to scan this repo and explain the architecture to me.

## Prompt 2:

I just ran these `bash npm create cloudflare@latest agents-starter -- --template=cloudflare/agents-starter npx wrangler@latest deploy ` next, I need to define your first Agent by creating a class that extends the Agent class.

## Prompt 3:

Nevermind, can we use the architecture from this repo instead? agents-starter

```markdown
// server.ts
// Change the imports

- import { openai } from "@ai-sdk/openai";

* import { createWorkersAI } from 'workers-ai-provider';

// Create a Workers AI instance

- const workersai = createWorkersAI({ binding: env.AI });

// Use it when calling the streamText method (or other methods)
// from the ai-sdk

- const model = openai("gpt-4o-2024-11-20");

* const model = workersai("@cf/deepseek-ai/deepseek-r1-distill-qwen-32b")
```

## Prompt 4:

Wait so the .dev.vars asks for a OPENAI_API_KEY but the project we are using Llama, so should I create a worker AI on cloudflare? Or how does it work? lets plan what we are doing. I'm trying to complete this task:
LLM (recommend using Llama 3.3 on Workers AI)
Workflow / coordination (recommend using Workflows, Workers or Durable Objects)
User input via chat or voice (recommend using Pages or Realtime)
Memory or state

## Prompt 5:

LLM isnt replying. [shows WebSocket connection error screenshot] lets revert back to the old working version.

## Prompt 6:

Create a list of major prompts I used in this conversation so far.
