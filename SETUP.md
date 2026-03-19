# Setup (5 minutes)

## Step 1: Duplicate the Notion template

Click [this link](TEMPLATE_URL) and duplicate it into your Notion workspace. This gives you all 8 databases, pre-wired relations, views, and documentation pages.

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

> **Note:** Connectors are available on all Claude plans, including Free. Once connected, the Notion integration works across all your Claude Projects — you only need to do this once.

## Step 4: Add the system prompt

1. Open `agent/system-prompt.md` from this repo ([view raw](../../raw/main/agent/system-prompt.md))
2. Copy the entire contents
3. In your Claude Project, click the ⚙️ gear icon at the top-right
4. Find **Instructions** and paste the system prompt

> **Alternative:** You can also download `system-prompt.md` and upload it directly to the **Files** section of your project instead of pasting into Instructions. Claude reads both.

## Step 5: Start a conversation

Type "hello" or "let's set up" — the agent will walk you through the rest.

It takes about 5 minutes. By the end you'll have:
- Your company name and project codes configured
- Your first real ticket and task in the system
- A config file to upload that makes the agent fully operational

The agent will generate a config file at the end of onboarding. Download it, then upload it to the **Files** section of your Claude Project (⚙️ gear icon → scroll to Files → click +). This is what switches the agent from onboarding mode to operations mode.

## Step 6 (Optional): Add agent patterns

Download [`docs/agent-patterns.md`](docs/agent-patterns.md) from this repo and upload it to your Project's **Files** section. This gives the agent a library of example prompts and workflows — so when you ask "what can you do?" it can suggest things like morning briefings, weekly rollups, triage runs, and more.

---

## What's in the box

| Component | What it does |
|-----------|-------------|
| **Notion template** | 8 interconnected databases with views, formulas, and relations |
| **System prompt** | Handles onboarding (first run) and daily operations (every run after) |
| **Config template** | Generated during onboarding with your real database IDs and project codes |
| **Agent patterns** | Copy-paste prompts for common workflows (optional but recommended) |

## Troubleshooting

**Agent can't find databases:** Make sure you duplicated the template (not just shared it). The agent needs the databases in your own workspace.

**Notion not showing in Claude:** Check that you've connected Notion at [claude.ai/settings/connectors](https://claude.ai/settings/connectors). You may need to refresh the page after connecting.

**Config file not detected:** Make sure you uploaded the JSON file to the **Files** section (⚙️ gear icon in the project), not as a message attachment. Start a new conversation after uploading.

**Relations broken after duplication:** This is rare but possible with Notion template duplication. The agent checks for this during onboarding and will tell you exactly what's wrong.
