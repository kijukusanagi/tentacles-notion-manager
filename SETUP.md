# Setup (15–30 minutes)

## Step 1: Duplicate the Notion template

Click [this link](https://tentacles-manager.notion.site/Tentacles-Management-Layer-3276026675c5817f9668eb0c557689fe) and duplicate it into your Notion workspace. This gives you the 8 core databases, pre-wired relations, views, and documentation pages.

## Step 2: Create a Claude Project

1. Go to [claude.ai](https://claude.ai) → Projects → New Project
2. Name it whatever you want (e.g., "Ops Agent")

## Step 3: Connect the Notion integration

1. Go to [claude.ai/settings/connectors](https://claude.ai/settings/connectors)
2. Click **Browse Connectors**
3. Find **Notion** under the Web category (or search for it)
4. Click the **+** icon next to Notion, then click **Connect**
5. You'll be redirected to Notion — log in and select the workspace where you duplicated the template
6. Authorize access and you're connected

> **Note:** Connectors are available on all Claude plans, including Free. Once connected, the Notion integration works across all your Claude Projects — you only need to do this once. If you don't see Projects in your sidebar, it may not be available on your plan.

## Step 4: Add the system prompt

1. Open `agent/system-prompt.md` from this repo ([view raw](../../raw/main/agent/system-prompt.md))
2. Copy the entire contents
3. In your Claude Project, click the ⚙️ gear icon at the top-right
4. Find **Instructions** and paste the system prompt

> **Alternative:** You can also download `system-prompt.md` and upload it directly to the **Files** section of your project instead of pasting into Instructions. Claude reads both.

## Step 5: Start a conversation

Type "hello" or "let's set up" — the agent will walk you through the rest.

It takes about 15–30 minutes. By the end you'll have:
- Your company name and project codes configured
- Effort tracking defaults and per-user capacity limits configured
- Your first real ticket and task in the system
- A config file to upload that makes the agent fully operational

The agent generates the config file at the end of onboarding. Then:

1. Save it under the name the agent suggests (e.g. `ac-tentacles-config.json`). The name itself doesn't matter — what matters is that the file's first key is `"tentacles_config": true`. The agent puts that there; don't remove it.
2. Upload it to the **Files** section of your Claude Project (⚙️ gear icon → scroll to Files → click +).
3. **Start a new conversation.** The agent detects the config and switches from onboarding mode to operations mode.
4. Optional but recommended: say `doctor`. It compares your config to your live Notion schema and shows any drift.

Your Notion documentation pages still have placeholder values at this point — that's intentional (filling them is the slowest part of setup). Say `update docs` whenever you want them filled in from the config.

## Step 6 (Optional): Add agent patterns

Download [`docs/agent-patterns.md`](docs/agent-patterns.md) from this repo and upload it to your Project's **Files** section. This gives the agent a library of example prompts and workflows — so when you ask "what can you do?" it can suggest things like morning briefings, weekly rollups, triage runs, and more.

### What goes in Files, and what doesn't

| Upload | File |
|---|---|
| **Yes** | Your generated config (`"tentacles_config": true`) |
| **Yes, recommended** | `docs/agent-patterns.md` |
| Only if migrating data | `docs/migration.md` |
| **No** | `agent/config-template.json`, `examples/sample-config.json`, `docs/v1.2-release-spec.md`, `docs/granular-dives-spec.md`, `docs/architecture.md`, anything in `docs/history/` or `docs/process/` |

The "No" files are for humans reading the repo. The template and sample carry placeholder or fake IDs and are marked `"tentacles_config": false` so the agent never mistakes them for your config — but the specs contain superseded schema blocks that can still mislead it.

---

## What's in the box

| Component | What it does |
|-----------|-------------|
| **Notion template** | 8 interconnected core databases with views, formulas, and relations — extensible with your own (see [`docs/extending.md`](docs/extending.md)) |
| **System prompt** | Handles onboarding, daily operations, effort tracking, proactive alerting, capacity planning, and structured deep dives |
| **Config template** | Generated during onboarding with your real database IDs, project codes, effort defaults, alert thresholds, and capacity limits |
| **Agent patterns** | Copy-paste prompts for common workflows including time tracking, health checks, sprint planning, and capacity management (optional but recommended) |

## Troubleshooting

**Agent can't find databases:** Make sure you duplicated the template (not just shared it). The agent needs the databases in your own workspace.

**Notion not showing in Claude:** Check that you've connected Notion at [claude.ai/settings/connectors](https://claude.ai/settings/connectors). You may need to refresh the page after connecting.

**Config file not detected:** Make sure you uploaded the JSON file to the **Files** section (⚙️ gear icon in the project), not as a message attachment, and that its first key is `"tentacles_config": true`. Start a new conversation after uploading.

**Relations broken after duplication:** This is rare but possible with Notion template duplication. The agent checks for this during onboarding and will tell you exactly what's wrong.
