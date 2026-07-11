# Weekly Goal Sheet — Management Reference

## What we built and why

The Weekly Goal Sheet was redesigned around one principle: **activity for its own sake doesn't close deals — real conversations and the outcomes that follow them do.** The tool is structured to make that visible every week, for every agent.

It has three parts:
1. **Lead Generation Tracker** — enforces the team standard of daily focused prospecting.
2. **Core Success Metrics** — the handful of numbers that actually predict production.
3. **Supporting Activities** — everything else, tracked for visibility but deliberately de-emphasized.

There's also a **paper worksheet** version for agents to use when they don't have a computer in front of them (in a coaching conversation, at a meeting), which they transfer into the online sheet afterward.

## The structure, in detail

**Lead Generation:** Every agent has a visible team standard — 9:30 AM–12:30 PM, 2-hour minimum daily. Agents log their actual time (supporting split blocks for days with conflicts), and get an unmissable visual flag (a large flashing X vs. a flashing flame/"Great Job!") showing whether they hit it that day.

**Core Success Metrics — the funnel that matters:**
- **Two-Way Conversations + Two-Way Text Conversations** combine into one shared goal. This was a deliberate call: raw call volume and raw texts-sent don't matter — genuine two-way engagement does. An agent sets one Monthly Goal (locked once entered) and a Weekly Goal (locked once entered) for the combined total of these two activities. If an agent under-commits a given week relative to their monthly pace, the tool shows a live warning with the exact make-up math (e.g., "you're 25 short this week, your remaining 3 weeks need to average 108/week").
- **Leads Generated, Appointments Set, Appointments Held, Listings Taken, Buyer Agreements Signed, Contracts Written/Offers Submitted, Closings** — each tracked individually with its own locked Monthly + Weekly goal. This completes the funnel from first conversation through closed transaction, which the original sheet didn't capture past "appointment held."

**Supporting Activities:** Calls Made, Emails Sent, Database Touches, FB/IG Engagement, Open Houses, Referrals, Reviews — still logged, but intentionally without goals or percentages. The reasoning: these are inputs or brand-building activities (FB/IG especially) whose ROI can't be cleanly tied to a weekly number, so we don't want agents gaming a goal that doesn't actually predict business.

**Why goals lock:** Once an agent commits to a Monthly or Weekly number and clicks away, it can't be edited — the only way to change it is a full "Start Fresh Week" reset, which zeroes that week's activity but carries the goal forward (unlocked) so it's not retyped from scratch. This was a deliberate choice to prevent quietly moving the goalposts after underperforming.

## Important limitation to know about

**This tool has no backend or central database — everything is stored locally in each agent's own browser.** Practically, that means:
- There is currently **no team-wide dashboard** where you can see every agent's numbers in one place. Each agent's data lives only on their own device/browser.
- Monthly rollups only work if an agent keeps using the same device/browser and types their name consistently — a different spelling or a different computer starts a fresh, empty history.
- If you want a **team-wide reporting view** (e.g., a manager dashboard showing every agent's pace at a glance), that would require adding a real backend/database and likely a login per agent — a meaningfully larger build than what exists today. Worth a conversation if it becomes a priority.

## Where it lives

- The live tool is deployed on Netlify at the team's existing site. (Note: deploying updates currently requires manually dragging the updated file into the Netlify dashboard, since this session's environment has a network restriction on Netlify's API — not a limitation of the tool itself.)
- The source file (`index.html`) and the paper worksheet (`worksheet.html`) are both in the `markitsoldhomesgroup/MARK-it-SOLD-Homes-Group` GitHub repo, on the `claude/kanban-board-revision-clecd7` branch.

## Rolling it out

1. Share the live link with agents, plus the agent-facing instructions.
2. Print or share the paper worksheet for agents who need it during in-person coaching or meetings without a laptop.
3. Emphasize the two things that matter most in review conversations: (a) is the 2-hour lead-gen window being hit daily, and (b) is the Two-Way Conversations pace on track for the month — the caution message does the math for you.
