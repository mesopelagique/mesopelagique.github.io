---
layout: post
title: "Jev: a decision model, not a chat model"
date: 2026-09-20
---

[Jev](https://typesafe.ai/) isn't a chat model — it's a **decision model for code**.

Instead of a free-form conversation, you ask a question, it answers with **probabilities**. You
give it a list of choices, it picks one.

For example, a customer writes:

```
"I was charged twice. Please fix this ASAP."
```

You want to know if it's a dispute:

```
noul: "Is this a billing dispute?"; {true: "The customer disputes a charge."; false: "Something else."}
```

Then categorize it:

```
choice: "What is this ticket about?"; {billing: Null; technical: Null; other: Null}
```

And how urgent it is:

```
score: "How urgent is this ticket?"; ["Not urgent."; "Somewhat urgent."; "Needs attention today."]
```

The response looks like this:

```json
"billing": { "type": "noul", "noul": 0.98 },
"category": {
  "type": "choice",
  "choice": "billing",
  "confidence": 1,
  "probabilities": { "other": 0, "technical": 0, "billing": 1 }
},
"urgency": {
  "type": "score",
  "score": 1.96,
  "confidence": 0.93,
  "legend": {
    "0": "Not urgent at all.",
    "1": "Somewhat urgent.",
    "2": "Needs attention today."
  },
  "probabilities": { "0": 0, "1": 0.04, "2": 0.96 }
}
```

From there, your code can escalate the ticket, create a task, tag it with a category, and so
on.

It's fast and cheaper than a chat model — and with the structured, typed response, you can
plug the result straight into your own logic instead of parsing free text.

## Jev with 4D

**[typesafe-sdk-4d](https://github.com/mesopelagique/typesafe-sdk-4d)** brings Jev to 4D.

A lot of things could be built on top of this (some of what's floating around online is "fake" —
it's a new buzzword), but paired with a standard LLM, it can do some genuinely useful work.

A curated list of resources: **[awesome-jev](https://github.com/kraayenjon/awesome-jev)** — use
cases, projects, SDKs, and more.

---

Find the full 4D component catalog on the [home page](/).
