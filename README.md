# Deploy and Host OpenClaw on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/openclaw-gateway?referralCode=sLjDXb&utm_medium=integration&utm_source=button&utm_campaign=openclaw-gateway)

[OpenClaw](https://github.com/openclaw/openclaw) is a personal AI agent you run yourself. It talks to you on Telegram, Discord, WhatsApp, Slack and other chat apps, remembers context, runs scheduled jobs and uses tools: shell, browser, files and GitHub. This template runs the official image with the gateway and its web **Control UI** behind a generated access token. Config, credentials, sessions and the agent workspace are stored on a volume.

## About Hosting OpenClaw

One service, one volume at `/data`. The official `ghcr.io/openclaw/openclaw` image runs pinned to a release. On boot, `openclaw doctor --fix` checks and repairs the state. The gateway then starts on port 8080, bound to the Railway network and protected by `OPENCLAW_GATEWAY_TOKEN`.

Before the gateway starts, the start command merges `OPENCLAW_RAILWAY_GATEWAY` into `openclaw.json`. That variable holds three settings:

- local mode
- your Railway domain as the public origin, so the browser UI is allowed
- Railway's proxy range, so logins through Railway's edge are accepted

## Common Use Cases

- A personal assistant on Telegram or Discord that works while your laptop is closed
- Scheduled agent jobs: briefings, monitoring, reports delivered to chat
- One always-on gateway for the OpenClaw desktop and mobile apps

## Dependencies for OpenClaw Hosting

- An LLM provider key (OpenRouter, Anthropic, OpenAI, or any other provider OpenClaw supports)
- Optional: a Telegram bot token from [@BotFather](https://t.me/BotFather), or Discord/Slack credentials

### Deployment Dependencies

- [OpenClaw on GitHub](https://github.com/openclaw/openclaw)
- [OpenClaw docs](https://docs.openclaw.ai/)

### Implementation Details

**First login:**

1. Open your Railway domain.
2. Paste `OPENCLAW_GATEWAY_TOKEN` from the service's Variables tab into **Gateway secret**.
3. A new browser also needs a one-time pairing approval. While the page waits, open the service's shell (right-click the service → **SSH**, or `railway ssh`) and run `openclaw devices list`, then `openclaw devices approve <requestId>`. The page connects by itself once you approve.

**Connect a model:** do one of the following:

- Set `OPENROUTER_API_KEY`, `ANTHROPIC_API_KEY` or `OPENAI_API_KEY` at deploy time, then pick the default model with `openclaw models set <provider/model>` in the shell.
- Run `openclaw onboard` in the service shell to choose a provider and default model.

**Telegram:** set `TELEGRAM_BOT_TOKEN`. New chats get a pairing request that you approve with `openclaw pairing list` and `openclaw pairing approve <code>`.

**Custom domain:** after adding it in Railway, change `publicOrigin` in `OPENCLAW_RAILWAY_GATEWAY` to `https://your.domain` and redeploy.

**Upgrading:** change the image tag in Settings → Source (e.g. `ghcr.io/openclaw/openclaw:2026.10.8` → a newer release) and redeploy. The volume keeps your data. Back it up first with `openclaw backup create` in the shell.

Notes and limits:

- **Needs about 1.3 GB of RAM** (Hobby plan or higher). The gateway and its worker processes use about 1.2 GB at idle, so the Free and Trial plans are too small.
- The first boot takes about a minute because the doctor sets up the state directory.
- The agent's shell and file tools run inside this container, as root, with the volume as their workspace.
- The token is an admin credential for an agent that can run commands. Keep it secret. To rotate it, change the variable and redeploy.

## Why Deploy OpenClaw on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying OpenClaw on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
