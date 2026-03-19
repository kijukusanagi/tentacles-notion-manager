# Setup Guide

Get your Tentacles operational backbone running in about 5 minutes.

## Prerequisites

- A Notion workspace (free or paid)
- A Claude Pro or Team subscription (required for Projects and the Notion MCP integration)

---

## Step 1: Duplicate the Notion Template

1. Click the template link: **[Duplicate into Notion →](https://tentacles-manager.notion.site/Tentacles-Management-Layer-3276026675c5817f9668eb0c557689fe)**
2. Click **Duplicate** in the top-right corner of the Notion page
3. Choose which workspace to duplicate into
4. Wait ~30 seconds for duplication to complete

All 8 databases, their schemas, views, formulas, and relations transfer automatically. You don't need to configure anything manually.

---

## Step 2: Create a Claude Project

1. Go to [claude.ai](https://claude.ai) → **Projects** → **New Project**
2. Name it whatever you want (e.g., "Ops Agent", "Tentacles", your company name)

**Connect the Notion MCP integration:**
- In the project, click the 🔌 integrations icon (or go to project settings)
- Search for "Notion" and click Connect
- Authorize access to your workspace when prompted — make sure you're authorizing the workspace where you duplicated the template

**Add the system prompt:**
- Open [`agent/system-prompt.md`](agent/system-prompt.md) from this repo
- Copy the **entire contents** — everything including the `<tentacles_operating_system>` tags
- In your Claude Project, go to **Settings → Custom Instructions**
- Paste the system prompt

---

## Step 3: Start a Conversation

Open your new Claude Project and start a conversation. Type `hello` or `let's set up`.

The agent will:
1. Find your Notion databases (~30 seconds)
2. Ask your company name and preferred project code prefix (~1 minute)
3. Discover your team members
4. Set up project codes in your Tickets database
5. Walk you through creating your first real ticket and task (~2 minutes)
6. Generate a config file for you to upload to Project Knowledge (~30 seconds)

---

## Step 4: Upload the Config

After onboarding, the agent generates a JSON config file.

1. In your Claude Project, go to **Settings → Project Knowledge**
2. Upload the config JSON file
3. The filename must match the pattern `*-config-*.json` (the agent generates it with the right name)

From now on, the agent automatically runs in production mode — no re-discovery, no re-configuration.

---

**You're done.** The agent is ready. Ask it to create tickets, spawn tasks, update statuses, or review your databases. It handles everything.

---

## Troubleshooting

See [`docs/troubleshooting.md`](docs/troubleshooting.md) for common issues.

If the agent can't find your databases, the most common cause is that the Notion MCP integration doesn't have access to the workspace where you duplicated the template. Check your project integrations and re-authorize if needed.
