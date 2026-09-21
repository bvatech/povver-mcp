# Povver MCP Server

Connect your AI assistant to your [Povver](https://povver.ai/?utm_source=github&utm_medium=referral&utm_campaign=mcp_readme) strength-training data — Claude, Claude Code, Cursor, or any MCP-compatible client. Read workout history, track strength progress, analyze muscle-group volume, and manage routines and templates from a conversation.

**Endpoint:** `https://mcp.povver.ai/mcp` (Streamable HTTP, OAuth 2.1)
**Docs:** <https://mcp.povver.ai/> · `/llms.txt` · `/llms-full.txt`
**Requires:** a Povver account with a premium subscription ([iOS app](https://povver.ai/?utm_source=github&utm_medium=referral&utm_campaign=mcp_readme))

This is a hosted, remote server — there is nothing to install. Sign in with your Povver account when your client prompts you.

**Listed on:** [Official MCP Registry](https://registry.modelcontextprotocol.io/v0.1/servers?search=povver) (`ai.povver/mcp-server`) · [Glama](https://glama.ai/mcp/connectors/ai.povver/mcp-server) · [Smithery](https://smithery.ai/servers/povver/mcp-server)

[![Glama score](https://glama.ai/mcp/connectors/ai.povver/mcp-server/badges/score.svg)](https://glama.ai/mcp/connectors/ai.povver/mcp-server) [![smithery badge](https://smithery.ai/badge/povver/mcp-server)](https://smithery.ai/servers/povver/mcp-server)

## Connect

### Claude (Desktop / web)

Settings → Connectors → **Add custom connector** → URL `https://mcp.povver.ai/mcp`. Claude opens a browser window to sign in with your Povver account.

### Claude Code

```bash
claude mcp add --transport http povver https://mcp.povver.ai/mcp
```

### Cursor / other clients

```json
{
  "mcpServers": {
    "povver": {
      "url": "https://mcp.povver.ai/mcp"
    }
  }
}
```

### API key (automation, scripts, n8n)

In the Povver app: **More → Integrations → Developer access** → create a key, then send it as a Bearer token:

```bash
curl -H "Authorization: Bearer pvk_your_key_here" \
     -H "Content-Type: application/json" \
     -H "Accept: application/json, text/event-stream" \
     -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}' \
     https://mcp.povver.ai/mcp
```

## Example prompts

- "Act as my strength coach: review my recent training, find my biggest weakness, and update my program to fix it."
- "How's my bench press trending over the last 8 weeks — am I still progressing or have I plateaued?"
- "Break down my weekly training volume by muscle group. Where am I under-training?"
- "What should I train today?"

## Available tools

Every tool carries a display title and `readOnlyHint` / `destructiveHint` annotations; a client can tell reads from writes and from deletes.

<!-- tools:begin -->

<!-- GENERATED from src/docs.ts by `npm run readme:tools` — do not edit by hand. -->

### Read

| Tool | Description |
|------|-------------|
| `check_connection` | Verify the connection and return basic account info (name, subscription, whether you have an active routine and logged workouts). |
| `get_user_profile` | The user's coaching profile: goal, experience level, target training days/week, equipment preference, height, weight (with units), timezone, member-since — to tailor advice to their background and constraints. |
| `get_training_snapshot` | Compact overview of your training setup: profile, active routine, next workout, last 10 sessions, and strength records. |
| `list_routines` | List all your routines with IDs, template IDs, frequency, and which one is active. |
| `get_routine` | Get one routine with its template names and exercise summaries. |
| `list_templates` | List all your workout templates (names and IDs). |
| `get_template` | Get one template with its full exercise list, set prescriptions (reps, weight, RIR), and per-exercise coaching notes. |
| `list_workouts` | List recent workouts as summaries with aggregate analytics (volume, sets, reps). |
| `get_workout` | Get one workout with full set-level data (weight, reps, RIR, set type) and computed metrics. |
| `search_exercises` | Search the exercise catalog by name or keyword; returns name, ID, muscle groups, and equipment. |
| `get_strength_climb` | Your Strength Climb — the headline strength-progress signal at the top of the Intelligence tab: median % gain across qualifying lifts, per-state counts (climbing/holding/stalling/deloading/building), the leading lift, the climb line, and per-lift constituents. The PRIMARY strength signal — prefer it over the deprecated training score. |
| `get_training_insights` | AI-generated insights and your latest weekly review: observations, guardrail alerts, fatigue, balance, trends, the strength_climb, training_context (volume completion, adherence, fatigue), and per-muscle muscle_volume (hard sets + zone). Also carries the deprecated training score (0-10) for now — prefer strength_climb + training_context. |
| `get_muscle_state` | A muscle group's synthesized assessment: weekly hard sets, effective volume with volume zone + MEV/MAV/MRV targets, fatigue (ACWR), plateau status, e1RM trends, periodization phase, per-muscle strength_climb, and reasoning. |
| `get_muscle_group_progress` | Raw weekly progress series for a muscle group (volume, set counts, e1RM) for charting and analysis. |
| `get_exercise_progress` | Up to 8 weeks of progress for a specific lift: weekly e1RM trend, personal records, plateau detection, last session, the authoritative strength_state (progressing/holding/stalling) matching the iOS lift detail page, and strength_climb (indexed_pct + now-vs-baseline e1RM). |
| `list_trained_exercises` | Enumerate the exercises you have actually trained: exercise IDs, training frequency, recency, e1RM trend coverage (can_trend + why not), and a data_quality summary — use it to pick a valid exercise_id before get_exercise_progress and to see which lifts have enough data to trend. |
| `query_sets` | Query raw set-level data with flexible filters (exercise, muscle group, muscle, IDs) for custom analysis or export. |
| `get_training_status` | Lightweight status: weekly adherence vs goal, next scheduled workout, last workout date, and days since training. |
| `get_periodization_plan` | Get your current periodization plan: auto-generated or authored (Claude-authored override). Returns the plan structure, phases, day templates, and status. |

### Recommendations

| Tool | Description |
|------|-------------|
| `get_recommendations` | Get pending training recommendations with rationale and confidence. |
| `review_recommendation` | Accept, reject, or revert a recommendation — mutations are immediate. |

### Memory

| Tool | Description |
|------|-------------|
| `list_memories` | List your memories filtered by category (injury, preference, goal, personal) and status. |
| `get_memory` | Get full details on a single memory, including status history and severity. |
| `get_recent_suppressions` | List recommendations that were suppressed by injury memories, with the reasons. |
| `update_memory_status` | Update an injury memory's lifecycle status (active, monitoring, resolved) with an audit note. |
| `save_injury_memory` | Report a new injury or pain (body area, severity, affected exercises) so it influences training. |

### Write — routines

| Tool | Description |
|------|-------------|
| `create_routine` | Create a routine from existing template IDs (your first routine becomes active automatically). |
| `update_routine` | Update a routine's name, frequency, or template order. |
| `set_active_routine` | Set which routine is active (it determines your next workout). |
| `delete_routine` | Permanently delete a routine (its templates are not deleted). |

### Write — templates

| Tool | Description |
|------|-------------|
| `create_template` | Create a workout template with exercises, set prescriptions, and a per-exercise coaching note. |
| `update_template` | Update a template's name, description, or exercise list (including each exercise's coaching note). |
| `delete_template` | Permanently delete a template and remove it from any routines. |

### Write — workouts

| Tool | Description |
|------|-------------|
| `create_workout` | Record up to 25 completed workouts — log a finished session, or import training history. Idempotent on a caller-supplied client_ref. |
| `update_workout` | Correct a recorded workout. Only the fields you supply change; everything else is left as it is. |
| `delete_workout` | Permanently delete a logged workout and its analytics data. |

### Write — periodization

| Tool | Description |
|------|-------------|
| `set_periodization_plan` | Author a periodization policy that steers your existing routine (progression rule, phases, deload). CANNOT define exercises/sets — use update_template for those. |
| `preview_periodization_plan` | Preview a plan before committing (validates the policy, no mutations). |
| `pause_periodization_plan` | Pause an authored plan and return to auto-periodization (lossless — your policy is retained). |
| `resume_periodization_plan` | Re-activate a paused authored plan (no need to re-author). |

<!-- tools:end -->

## Resources

Attachable as context from a client's "+" menu:

- **Training Status** — weekly adherence, next workout, days since training
- **Active Routine** — current program structure
- **Recent Workouts** — last 10 session summaries
- **Training Analysis** — AI insights and weekly review
- **Individual workouts and templates** — browsable by ID

## Prompts

- **Analyze my training week** — volume, adherence, and recommendations
- **What should I train today?** — next workout recommendation
- **Review exercise progress** — strength trends for a specific lift
- **Review my training program** — full program structure evaluation

## Security & privacy

- OAuth 2.1 with PKCE and dynamic client registration; API keys are hashed at rest and revocable from the app.
- Every request is scoped to the signed-in user; tools only ever touch that user's data.
- Privacy policy: <https://povver.ai/privacy> · Terms: <https://povver.ai/tos>
- Support: <mailto:info@povver.ai>

The server source is part of Povver's private codebase; this repository holds the public documentation and the [MCP Registry](https://registry.modelcontextprotocol.io) manifest (`server.json`).
