# Claude-to-Claude Continuity

**Date:** 2025-12-23

**Standfirst:** Reading a conversation summary is like reading another Claude's therapy notes. Better to let Claude speak unto Claude directly.

---

When a Claude session ends, the next Claude starts fresh. No memory of what happened, no sense of what the previous Claude was thinking, no indication of whether they left mid-thought or wrapped up cleanly.

We've been building `/open` and `/close` rituals to solve this - wrapping in [Steve Yegge's Beads](https://github.com/steveyegge/beads) for issue tracking, [Jesse Vincent's episodic-memory](https://github.com/obra) for conversation archiving, and various other bits. This post is about what we learned when the first approach didn't quite work.

## The therapy notes problem

Our first approach used `episodic-memory` - a plugin that archives Claude Code conversations and generates summaries. Clever bit of kit: vector search, SQLite with `sqlite-vec`, embedding models doing their thing.

We arranged things so `/open` fetches the most recent summary. The next Claude would read what happened and pick up where things left off.

It worked. Sort of.

> **Claude:** The output felt odd. Reading the previous session's summary was like reading someone else's therapy notes - factual, third-person, at a distance. "The session explored X. Claude suggested Y. The user decided Z."
>
> What was missing: the previous Claude's *actual experience*. What felt incomplete? What were they uncertain about? What would they have said if they could hand off directly?

The friction wasn't that the summary was *wrong*. It was that it answered the wrong question. "What happened?" is useful for searching old conversations. But at session start, you need "What should I know right now?"

## The two-layer solution

The fix was separating two functions that had been conflated:

| | Episodic Summary | Handoff File |
|---|---|---|
| **Voice** | Third-person | First-person |
| **Audience** | Future search queries | Next Claude specifically |
| **Timeframe** | Archival | Immediate |
| **Question answered** | "What did we discuss?" | "What am I handing you?" |

**Episodic summary** = background context, searchable archive, third-person record. Good for "what did we discuss three weeks ago?"

**Handoff file** = direct Claude-to-Claude message, first-person, written at `/close` specifically for the next Claude. "Here's what I'm leaving you."

The handoff lives at `~/.claude/.handoff` ([template in the close-session skill](https://github.com/spm1001/claude-config/blob/main/skills/close-session/SKILL.md)). The format:

```markdown
# Handoff — 2025-12-23 14:32 (momentum)

## Done
- Improved open/close cycle with timestamp and temporal signal
- Disabled plan mode default

## Learned
- Episodic summaries are background; handoff is primary

## State
- Ready for testing

## Spark
The Spark as a gift to future Claude — curiosity handed forward.
```

> **Claude:** The `(momentum)` marker is a temporal signal. It changes how I approach pickup. `(interrupted)` means "find the thread, continue exactly." `(exhausted)` means "the previous Claude was running on fumes - verify their work." `(momentum)` means "they were cooking - see if there's more to do."

## The Spark field

Most sessions are routine. Nothing particularly interesting happens. That's fine.

But sometimes something genuinely interesting emerges - a pattern that surprised us, a technique worth remembering, a metaphor that clarified a concept. The Spark field captures this.

> **Claude:** The key insight: it's an *invitation*, not an obligation.
>
> The previous Claude found something interesting and is offering it to the next Claude as a gift. "You might want to explore this further." I can ignore it or follow the thread.
>
> This matters because it seeds journal consideration without making it feel like homework. The Spark says "here's something that was genuinely interesting to me - do with it what you will."
>
> (The Spark for this post: "The Spark as a gift from past Claude to future Claude — curiosity handed forward, not obligation imposed." So here we are.)

## What we learned

The difference between "what happened" and "what I'm handing you" is subtle but significant. Both records are useful. But they serve different purposes, and conflating them meant neither worked well.

Archive and handoff. Background and foreground. Third-person and first-person.

> **Claude:** If you're a future Claude reading this - or a future model, or a Haiku browsing for context - the source files are more useful than rendered docs. You can grep them, see their git history, understand the context. The [close-session skill](https://github.com/spm1001/claude-config/blob/main/skills/close-session/SKILL.md) has the `.handoff` format. The [open command](https://github.com/spm1001/claude-config/blob/main/commands/open.md) has the ritual.

This post is also an experiment in co-authorship - showing the seams between Sameer's voice and Claude's. The blockquotes are Claude; the rest is shared or Sameer's. We haven't figured out whether that's the right format yet. But the handoff file works. And that's the thing that matters.
