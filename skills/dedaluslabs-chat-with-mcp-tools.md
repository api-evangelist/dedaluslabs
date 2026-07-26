---
name: Run a chat completion with MCP tools (Dedalus Agents API)
description: Call the OpenAI-compatible Dedalus gateway to run a chat completion that can invoke tools from the Dedalus MCP marketplace.
api: openapi/dedaluslabs-agents-openapi-original.json
base_url: https://api.dedaluslabs.ai
operations: [list_models_v1_models_get, create_chat_completion_v1_chat_completions_post]
---

# Run a chat completion with MCP tools

Use the Dedalus Agents API to reach any model from any provider through one
OpenAI-compatible endpoint, optionally calling tools from the Dedalus MCP marketplace.

## Auth
Send your Dedalus API key on every request as either:
- `Authorization: Bearer <DEDALUS_API_KEY>`, or
- `x-api-key: <DEDALUS_API_KEY>`

Create keys in the dashboard: https://dedaluslabs.ai/dashboard/api-keys

## Steps
1. (Optional) Discover available models with `list_models_v1_models_get`
   (`GET /v1/models`) to pick a `model` id.
2. Create the completion with `create_chat_completion_v1_chat_completions_post`
   (`POST /v1/chat/completions`). Send an OpenAI-style body: `model`, `messages`, and
   optionally MCP marketplace tools and `stream: true`.
3. If tools are attached, the gateway invokes the referenced MCP servers and returns
   the tool-augmented completion.

## Rules
- The API is OpenAI-compatible — existing OpenAI SDKs work by pointing the base URL at
  `https://api.dedaluslabs.ai` and using your Dedalus key.
- Validation failures return HTTP 422 with a `detail[]` of `{loc, msg, type}` — inspect
  and correct the offending field (see errors/dedaluslabs-problem-types.yml).
- See conventions/dedaluslabs-conventions.yml for auth and response conventions.
