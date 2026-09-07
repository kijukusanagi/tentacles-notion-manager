# Troubleshooting

---

## Agent can't find databases

**Symptom:** The agent says it can't find the OS Layer page or can't locate the 8 core databases.

**Cause:** The Notion MCP integration isn't connected, or it's authorized for the wrong workspace.

**Fix:**
1. In your Claude Project, go to Settings → Integrations
2. Check that Notion is listed and connected
3. If connected but wrong workspace, disconnect and re-authorize — make sure you select the workspace where you duplicated the template
4. Try again: `let's set up`

---

## Relations point to wrong databases after duplication

**Symptom:** After duplicating the template, some relation properties on Tickets (or other databases) still point back to the original template's databases instead of your duplicated copies.

**Cause:** Rare Notion bug during duplication — occasionally relations don't get re-wired correctly.

**Fix:**
1. In Notion, open the Tickets database → Edit properties
2. For each relation property (Related Tasks, Related Engagement, etc.), click on it and check which database it points to
3. If it points to the wrong database, change it to the correct duplicated database
4. Repeat for any other databases with broken relations
5. After fixing, run `reconfigure` so the agent re-discovers the corrected IDs

---

## Enum values fail silently

**Symptom:** The agent sets a select field but the field stays empty with no error.

**Cause:** The string value doesn't exactly match one of the valid enum options.

**Fix:** Check [`docs/enum-reference.md`](enum-reference.md) for the exact string values. Pay attention to capitalization, spacing, and special characters (e.g., `"100% = High"` not `"High"`). The Notion API does not return an error for invalid select values — it just ignores them.

---

## Agent creates tickets in wrong teamspace

**Symptom:** Tickets or pages are appearing in the wrong Notion teamspace.

**Cause:** You have multiple teamspaces and the agent found an "OS Layer" page in a different one.

**Fix:**
1. Type `reconfigure` to restart onboarding
2. When the agent finds multiple OS Layer pages, it will ask which teamspace to use — specify the correct one
3. The agent will scope all subsequent operations to that teamspace only

---

## Config file not detected

**Symptom:** The agent keeps running onboarding even though you've already set up.

**Cause:** The config file isn't in the project's Files, or it doesn't carry the marker `"tentacles_config": true` as a top-level key. (Since v1.4 the filename doesn't matter — the marker does. `config-template.json` and `sample-config.json` carry `false` on purpose and will never be treated as a config.)

**Fix:**
1. In your Claude Project, click the ⚙️ gear icon → Files
2. Check if the config file is there
3. If not, retrieve the config from your onboarding conversation (the agent outputs it as a code block) and re-upload it
4. Open the file and confirm the first key is `"tentacles_config": true`. If it's missing (a pre-v1.4 config), say `doctor` — the agent will offer the v1.3 → v1.4 migration which adds it — or add the line by hand
5. Start a new conversation after uploading

**Agent refuses with "still contains placeholder values":** You uploaded a template, or a config that was never fully generated. Remove it and run onboarding (`set up`) to generate a real one.

**Agent asks which config is current:** Two or more files in Files carry `"tentacles_config": true`. Remove the stale one.

---

## Date fields not setting correctly

**Symptom:** Date properties appear empty after the agent sets them.

**Cause:** Using plain date strings (`"2026-04-01"`) instead of the expanded Notion MCP format.

**Fix:** The agent handles date formatting automatically in operations mode. If you're writing MCP calls manually, use the expanded format:

```json
"date:Due Date:start": "2026-04-01",
"date:Due Date:end": null,
"date:Due Date:is_datetime": 0
```

See [`docs/architecture.md`](architecture.md) for the full date format documentation.

---

## Agent keeps running onboarding

**Symptom:** Every new conversation starts with the onboarding flow instead of operating normally.

**Cause:** The config file is missing from the project's Files, or lacks the `"tentacles_config": true` marker. The agent checks for a marked JSON file on every session start — if none is there, it defaults to onboarding mode.

**Fix:** Upload the config JSON to the project's Files (⚙️ gear icon → Files). If you don't have the config file anymore, run onboarding once more — it takes about 15–30 minutes and the agent will regenerate it.
