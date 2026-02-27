# WhatsApp AI Agent Bridge

A lightweight bridge that connects WhatsApp (via venom-bot) to an n8n workflow, so messages are forwarded to an LLM (OpenRouter) and replies are sent back to WhatsApp. Includes a local Express endpoint to simulate self messages for testing.

## Features
- Receives WhatsApp messages with `venom-bot`
- Forwards content to an n8n webhook
- Generates replies via OpenRouter in n8n
- Sends replies back to WhatsApp
- Test endpoint for simulating self messages

## How It Works
1. `venom-bot` receives a message.
2. `bot.js` posts `{ body, from }` to the n8n webhook.
3. The n8n workflow runs the LLM chain and returns `{ reply }`.
4. The reply is sent back to WhatsApp.

## Prerequisites
- Node.js and npm
- Docker (for n8n)
- OpenRouter account and API key

## Setup
1. Start n8n with Docker:
```bash
docker pull n8nio/n8n
docker run -it --rm -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n
```

2. Open n8n at:
```text
http://localhost:5678
```

3. Import `AI_Bot.json` into n8n.

4. Configure the OpenRouter credentials in the workflow.

5. Enable the workflow.

6. Install dependencies:
```bash
npm install
```

7. Run the bot:
```bash
node bot.js
```

## Webhook Notes
- `bot.js` currently posts to:
```text
http://localhost:5678/webhook-test/webhook/whatsapp
```
- Use the `webhook-test` URL while using n8n’s “Test” mode.
- Switch to:
```text
http://localhost:5678/webhook/whatsapp
```
  when the workflow is active.

## Test Endpoint
Send a simulated message:
```bash
curl -X POST http://localhost:3000/simulate-self \
-H "Content-Type: application/json" \
-d '{"body":"Hi","from":"your-phone-number@c.us"}'
```

## Configuration
- Webhook URL: `bot.js`
- Express port: `PORT` in `bot.js`
- Session name: `session` in `bot.js`

## Project Files
- `bot.js` — WhatsApp bridge and Express server
- `AI_Bot.json` — n8n workflow
- `tokens/` — session data

## Troubleshooting
- If no reply is sent, verify the webhook URL and n8n workflow status.
- If n8n is not responding, confirm Docker is running and port `5678` is exposed.
- If WhatsApp login fails, delete `tokens/` and restart to re-pair.
