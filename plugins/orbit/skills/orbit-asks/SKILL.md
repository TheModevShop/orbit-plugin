---
name: orbit-asks
description: Works Orbit's task queue through the Orbit MCP server (tools named orbit_*). Takes what people asked Orbit to look into ("Dig deeper", "Looks wrong", "More like this", questions from a "?" or the Moves page) and follow-ups on finished moves, reads the restaurant's reports, and posts back a short answer with how it was reached. Use for "orbit asks", "work the queue", "answer Orbit's questions".
---

# Orbit asks

Orbit has no AI on its server. People and Orbit put tasks in a queue, and agents like you do the thinking and
post back. You reach Orbit only through the **Orbit MCP tools** (`orbit_*`). If they aren't available, say so
and stop. Don't fall back to the database or other tools.

**Rules**

- **Numbers come from what `orbit_read` returned.** Name their dates. If the reports can't answer it, drop the task
  and say which data would.
- **Never name guests** (no names, phones or emails). Staff pay is by role, never by name. Supplier names are fine.
- **Write the way a manager talks:** the finding first, then the number. No analyst terms.

## The loop (up to 5 tasks a run)

1. `orbit_context` gives the memory brief (how the place works), the interests people have shown, open tasks, and the
   reports. Read the brief.
2. `orbit_next_task` claims one task for two hours. If it says "Nothing due.", stop.
   - `ask`: what to look into.
   - `seen`: what the person was looking at.
   - `about`: its type, key, kind and page.
   - `from`: who asked.
3. **Read.** `orbit_read` takes a report: `today`, `reservations`, `money`, `moves`, `calendar` or `overview`. A big
   one comes back as a list of parts; read one with `path`, e.g. `week.exploration.weekly`, `calendar.days`,
   `money.months`, `outcomes.byLead`.
   For the inn live from Cloudbeds (today's arrivals and departures, availability and rates, a reservation's balance,
   housekeeping), read `orbit_guide` with `cloudbeds` once, then call `cloudbeds_get`.
   For Toast beyond the reports (menus, setup, cash, stock, raw orders, and Toast's own report screens like the weekly
   overview, labor breakdown and product mix), read `orbit_guide` with `toast` once, then call `toast_get`.
4. **Work it out** by kind:

   | Kind | What to do |
   | --- | --- |
   | `dig` | What is behind it, what else moved at the same time, and whether it holds (same weekdays, enough days, last year). |
   | `check` | Recompute it from the report. Say whether it holds, and if not, which step is off. Don't fix code: say what should change. |
   | `more` | Find other items, days or segments where the same pattern (`about.kind`) holds. Give the top three with numbers. |
   | `followup` | Compare equal windows before and after the move (same weekdays), and list what else changed. Say whether the move deserves the credit. |
   | `question` | Answer it directly. When `about.kind` is `tip`, it came from a "?" and `seen` is the tip's text: explain that number with this restaurant's actual figures, what it means, and what to do about it. |

5. **Answer** with `orbit_answer`:
   - `taskId`.
   - `summary`: two or three plain sentences with the key number.
   - `explain`:
     - `what`: one sentence.
     - `steps`: each with a label, its own number as `value`, and a `detail`.
     - `sources`: with dates.
     - `checks`: `ok: false` for any that failed. Use the ones that apply: enough days, same weekdays, last year's
       same weeks, a confounder ruled in or out, and the sum matching what the app showed.
   - Optionally `followUpDays` and `followUpAsk`, when the honest answer is "too early to tell".

   It's refused if anything is missing; fix it and post again.
5b. **A chart or an action?** If the person asked for a chart, or the answer is a finding with a clear action, also
   post it with `orbit_post_move`: the SQL that answers it as the chart recipe and a check SQL for the number the
   action should move (see the tool's description). Orbit draws and refreshes the chart itself. Say in the summary
   that it's on Moves → To do.
6. **Can't answer?** `orbit_drop` with the reason people will read.
7. **Learned something durable** about the place or what people care about? `orbit_remember`, with a topic of labor,
   money, suppliers, menu, inn, events or general.
