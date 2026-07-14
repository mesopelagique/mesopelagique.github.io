---
layout: post
title: "FormElementTransition — one element, two states, and the flight in between"
date: 2026-07-14
component: FormElementTransition
component_url: https://github.com/mesopelagique/FormElementTransition
version: 0.0.1
---

Something graceful is surfacing from the deep. On the web and on mobile there is a well-known
trick: the same element — a thumbnail, a card, a title — exists in two screens, and instead of
cutting between them, it *glides*. It moves, resizes, reshapes, recolors. You know it as **Hero
animations** (Flutter/iOS), **Magic Move** (Keynote), **matchedGeometryEffect** (SwiftUI), or a
**Container Transform** (Material Design). The generic name is a **shared element transition**.

**[FormElementTransition][repo]** brings it to 4D forms — a small component, namespace `cs.hero`,
driven by the plain form timer at ~60 fps.

## The idea

Two form objects represent the *same* thing in two states. A little blue rounded card and a big
orange one; a row and its detail panel; a login avatar and the tiny avatar it becomes in the home
header. Only one is visible at a time. The engine animates the second one out of the first one's
place — position, size, corner radius, font size, and colors all interpolated together.

## Setup

It's a component, so add it to your dependencies — no copying classes around. Then wire the form
timer once:

```4d
Case of
	: (Form event code=On Load)
		Form.transition:=cs.hero.ElementTransition.new()

	: (Form event code=On Timer)
		Form.transition.onTimer()
End case
```

Enable the **On Timer** event on the form, and everything else is a one-liner from your object
events.

## A hero transition

```4d
// The big card flies out of the small card's place
Form.transition.share("cardSmall"; "cardLarge"; {duration: 450; easing: "easeOutBack"; colorMode: "hsv"})

// …and back — just swap the names
Form.transition.share("cardLarge"; "cardSmall"; {duration: 350; easing: "easeInOutCubic"; colorMode: "hsv"})
```

There are eleven easing curves (`easeOutBack`, `easeOutBounce`, `easeOutElastic`…), a fluent
builder for free-form tweens, and a `morph()` variant where the source object itself travels and
reshapes into the destination (a Material *container transform*).

## Between two real forms

The realistic case: you validate a form and a **second form** takes its place in the same window.
Elements are matched **by object name** — the closing form snapshots them, the next form makes the
matching objects fly on load:

```4d
// Form A, in the event that closes it
Form.hero:=Form.transition.capture(["avatar"; "userName"; "header"])
ACCEPT

// Form B, On Load
If (Form.hero#Null)
	Form.transition.heroFrom(Form.hero; {duration: 450; easing: "easeOutCubic"})
End if
```

The snapshot is a plain, JSON-serializable collection, so it rides along in the `DIALOG` form data
(or a file, if you like). The bundled `DEMO_TwoForms` shows a full login → home flight, and back
on logout.

## About that greenish flicker

Interpolate two distant hues component-by-component in RGB — blue → orange — and the midpoint is a
muddy, slightly *green* grey. Mathematically correct, visually unpleasant. Pass `colorMode: "hsv"`
and the color travels the **shortest path around the hue wheel** instead, staying vivid the whole
way: blue → violet → magenta → red → orange. Automatic/system colors are left alone and snap at
mid-course.

## The honest limits

4D form objects have no opacity property, so a cross-fade is approximated by color interpolation
plus a visibility swap at the end of the flight. Corner radius only animates between two rectangle
objects. And since the engine uses `SET TIMER`, every call must come from the form's own context.
Small caveats for a genuinely fun little effect.

The classes each carry their own reference doc (`ElementTransition`, `ElementAnimation`,
`ElementState`), shown right in the 4D code editor. Two demos ship in the box: `DEMO` (single
form) and `DEMO_TwoForms` (two forms).
---

Grab it on [GitHub][repo], browse the full 4D component catalog on the [home page](/).

[repo]: https://github.com/mesopelagique/FormElementTransition
