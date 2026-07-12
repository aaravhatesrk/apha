# Alpha Phoenix

A conversational AI assistant with photo understanding, built on n8n. Text is handled by Groq (`llama-3.3-70b-versatile`); messages with an attached photo are routed to Google Gemini (`gemini-2.5-flash`). Both paths share one conversation memory.

## Contents

- `render.yaml` — Render Blueprint defining the n8n service and the static chat UI
- `alpha-phoenix-workflow.json` — the n8n workflow (import this after n8n is deployed)
- `index.html` — the chat interface

## Deploy steps

1. In Render, create a new **Blueprint** pointing at this repo. It provisions two services from `render.yaml`: `alpha-phoenix-n8n` (n8n itself) and `alpha-phoenix-ui` (this chat page).
2. `render.yaml` uses the `starter` plan with a persistent disk for n8n — Render's free tier has no persistent disk, so workflows/credentials would be lost on every restart.
3. In the Render dashboard, set `N8N_ENCRYPTION_KEY` on the n8n service (kept out of this repo intentionally).
4. After first deploy, update `N8N_HOST` and `WEBHOOK_URL` in `render.yaml` to match the real `.onrender.com` address Render assigns, then redeploy.
5. Open the hosted n8n, import `alpha-phoenix-workflow.json` (Workflows → Import from File), then add your Groq and Google Gemini API credentials and connect them to the "Groq Model" and "Gemini Vision Model" nodes. Activate the workflow.
6. Copy the production webhook URL n8n shows you and paste it into the `WEBHOOK_URL` constant near the top of `index.html`'s `<script>` block. Commit and push — the static site redeploys automatically.

## Security note

The webhook currently has no authentication, so anyone with the URL can use it once deployed. Consider adding header-based auth on the "Chat Webhook" node before sharing the URL publicly.
