# proactive

This repository exists to answer one question:

> **What is the minimum architecture capable of generating useful unsolicited observations?**

Everything here is subordinate to that question. If a file, dependency, or
abstraction does not move us toward answering it, it does not belong.

---

## Definition of success

The system occasionally produces:

> "I think we should do X because Y, which will likely create Z."

**without being directly asked.**

That's it. Not often. Not reliably. Not about everything. Occasionally, and
unprompted. The word *unsolicited* is carrying most of the weight in that
sentence — a system that produces this on request is a chatbot, and chatbots
already exist.

---

## What is initiative?

Initiative is what happens when a system speaks without being spoken to, and
what it says is worth hearing.

That decomposes into two tractable problems:

1. **A scheduling problem.** Something has to decide *when* to think. Nobody
   asked. There is no incoming request to react to. Absent a clock, there is no
   initiative — only response.
2. **A question problem.** Once it is thinking, it has to be pointed at a
   question good enough that the answer is worth interrupting someone for.

Both are engineering problems. Neither requires intelligence we don't have.
This is the central bet of the repo: **initiative is not a capability problem,
it is an architecture problem.** The models are already good enough to notice
things. They are simply never running when nobody is talking to them, and when
they do run they are asked to summarize instead of to disagree.

---

## What we are not building

- **Not AGI.**
- **Not consciousness.** No claims about experience, awareness, or interiority.
- **Not a chatbot.** There is no conversational surface. Nobody talks to this.
- **Not a memory system.** No embeddings, no vector store, no retrieval layer,
  no recall API. The trace is an append-only log read in full.
- **Not a knowledge graph.** No entities, no relations, no ontology.
- **Not an agent framework.** No tools, no planning loop, no delegation.

These exclusions are the most important section in this document. Each one is
a well-lit road that leads somewhere other than initiative, and each one is
*easy* — there are libraries, tutorials, and a decade of blog posts waiting.
Drift toward them will feel like progress. It isn't.

**Litmus test for any proposed addition:** does it help the system say
something nobody asked for? If the honest answer involves the word
"eventually," the answer is no.

---

## Core beliefs

1. **Initiative is a scheduling problem plus a question problem.**
2. **Insight is expectation minus observation.** Nothing is interesting on its
   own. Things are interesting because they differ from what you'd have
   predicted. A system with no expectations cannot be surprised, and a system
   that cannot be surprised has nothing to report.
3. **Memory without delivery is dead storage.** An observation that is
   generated and not delivered did not happen.
4. **A summary is not an initiative.** Summaries compress what you already
   know. They are the default failure mode of every system like this, because
   they are always available and always look like output.
5. **A good observation should be capable of changing a decision.** If knowing
   it changes nothing anyone does, it is trivia.
6. **The system must prefer disagreement over agreement.** Agreement is free
   and carries no information. The observations worth the interruption are the
   ones that say *this is going wrong* or *you are assuming something false.*
7. **One useful observation is worth more than 100 summaries.** The correct
   output rate is low. Silence is a valid and often correct result.

---

## V1 architecture

Four layers. No more.

```
L0  Clock      when to think        — fires unprompted; the source of initiative
L1  Trace      what happened        — append-only event log, read in full
L2  Question   how to think         — expectation vs. observation → one candidate
L3  Delivery   who hears it         — writes observations/latest.json
```

**L0 — Clock.** Fires on a schedule. Owns nothing else. This layer is the only
reason anything here is unsolicited; without it the repo is a library.

**L1 — Trace.** An append-only record of events. Appended to, read whole,
never queried, never indexed, never embedded, never summarized into itself.
When the trace outgrows a full read, that is a real problem to solve then —
and solving it early is how this becomes a memory system.

**L2 — Question.** Takes the trace, forms an expectation, compares it to what
actually happened, and produces at most one candidate observation. This layer
is where the repo either works or degenerates into summarization. Its behavior
lives in `prompts/reflection.md`, which is a design document with as much
standing as this one.

**L3 — Delivery.** Writes the observation where it will be seen. Per belief 3,
this layer is not optional plumbing — it is the layer that makes the
observation real.

---

## Success criteria for V1

- Append events to the trace.
- Run reflection.
- Produce exactly one observation.
- Write it to `observations/latest.json`.

Nothing else.

When those four things work end to end, V1 is done, and the next question is
whether the observations are any good — not whether the architecture can be
extended.

---

## Repository layout

```
README.md                 this document — why the repo exists
prompts/reflection.md     how the repo thinks — the L2 contract
observations/latest.json  the single most recent observation (L3 output)
```

---

## A note to future sessions

You are probably here to add something. Before you do, re-read *What we are
not building*. The most likely reason this repo fails is not that the idea was
wrong — it is that fifty reasonable-looking commits turned it into a
retrieval-augmented agent framework that never says anything unprompted.

The philosophy is documented before the implementation deliberately. When the
two conflict, the philosophy wins, and the implementation is the thing that
changes.
