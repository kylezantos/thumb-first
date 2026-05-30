# Content & Attention

What goes on the screen, in what priority, and how it survives a distracted, glancing user. Lenses: **Focus Forcer (Wroblewski)**, **Empirical Skeptic (Budiu / NN/g)**, supported by **Thumb-Zone Mapper**.

Load this when deciding content layout, hierarchy, prioritization, or reviewing a cluttered screen.

---

## Prioritization Is the Design Work

Wroblewski's central claim: the small screen is not a problem to solve, it is a **forcing function**. It makes you answer the question every screen should answer anyway — *what actually earns this space?*

The method:
1. **List everything** the screen currently shows or could show.
2. **Rank ruthlessly** — what is the *one* thing this screen is for? Then second, then third.
3. **Cut, defer, or demote** the rest. Defer = behind a tap, lower on scroll, or on a later screen. Cut = gone.
4. **Lay out from the ranking.** Layout is downstream of priority, never the other way around.

The mobile-to-desktop direction is the payoff: the priority you were forced to find on mobile is the right priority on *every* size. That is "mobile first" in its real sense — a prioritization discipline, not a CSS breakpoint.

> **Claude's failure mode:** taking a desktop screen and shrinking it — every element preserved, just smaller and stacked. That is not mobile design. If nothing was cut or demoted, no prioritization decision was made.

---

## How People Read on Mobile (Budiu / NN/g)

Mobile users scan more than they read. NN/g's eye-tracking identifies the patterns — design for the scan:

| Pattern | What the eye does | Design implication |
|---------|-------------------|--------------------|
| **Layer-cake** | jumps heading to heading, skips body | the most effective scan — make headings carry the meaning on their own |
| **Spotted** | hunts for bold words, links, numbers | make the *important* words visually distinct, not the decorative ones |
| **F-pattern** | front-loads first words of lines | put information-carrying words first; don't open every line with filler |
| **Commitment** | full reading | only happens with trust or high motivation — never assume it |
| **Marking** | eyes fixed, thumb scrolls | common on mobile — content streams past a fixed gaze; rhythm and chunking matter |

**Consequences for layout:**
- **Headings and subheadings do the heavy lifting.** A user who reads only the headings should still get the gist. Vague headings ("Overview," "More") waste the layer-cake scan.
- **Front-load.** First sentence, first words, first screen — the answer up top, detail below.
- **Chunk.** Short paragraphs, clear groups, generous spacing between groups. A wall of text on a phone is abandoned, not read.
- **Visual distinction is a budget.** If everything is bold, nothing is. Spend emphasis on the words that carry meaning.

---

## Glanceability — Designing for Interrupted Attention

Mobile use is interrupted by default: walking, in line, mid-conversation, one eye on something else. The 2010 canon under-covered this because small screens were already simple. On today's large screens, glanceability is a deliberate decision.

Borrow from instrument-panel design — encode state so it's readable in the *periphery*, before it's read as text:

- **Status as position, color, shape, size** — not as a sentence the user must stop and read. A user should know the state from a glance, then read for detail only if they choose.
- **One primary thing per screen.** A glanceable screen has an obvious focal point. Competing focal points force a stop-and-study the interrupted user won't give.
- **Survive the resumed glance.** When the user looks back after a distraction, the screen should re-orient them instantly — clear title, stable layout, obvious "where am I."
- **Respect peripheral readability** — sufficient size and contrast for the important state; don't hide critical status in low-contrast fine print.

---

## Overlay Proliferation — the Current #1 Failure (Budiu)

NN/g's recent finding: the dominant mobile UX problem today is not forms or content parity — it is **too many layers**. Stacked sheets, popups over modals, in-app browsers with their own chrome, banners over toasts over dialogs.

The damage: the user loses track of *where they are* and *what dismisses what*. A swipe-down means "close the sheet" in one layer and "open Control Center" in another. Accidental dismissals lose work.

**Rules:**
- **One overlay at a time.** Resolve the current layer before opening another. If a flow seems to need stacked overlays, it needs to be a sequence of *screens*, not a stack of *layers*.
- **Every layer has an obvious, consistent dismissal** — and not gesture-only (see `touch-gesture-motion.md`).
- **Prefer a screen over an overlay for anything that isn't transient.** Overlays are for quick, scoped, dismissible moments. A multi-step task is a flow of screens.
- **Watch in-app browsers** — opening web content inside the app stacks a second set of navigation chrome. Decide deliberately: in-app browser vs. hand off to the system browser.

---

## States: Empty, Loading, Error

A screen is not designed until its non-ideal states are designed. These are content decisions, not edge cases.

- **Empty state** — never a dead end. Explain why it's empty in plain language and give the one obvious action forward ("No results — clear filters"; "No messages yet — start a conversation"). An empty screen with no path is a design omission.
- **Loading state** — match the indicator to the wait. Skeleton screens (content-shaped placeholders) for content loads — they set the layout expectation and feel faster. A determinate progress bar when duration is known; a spinner only when it isn't and the wait is short. Avoid layout shift when real content arrives.
- **Error state** — say *where* the error is, *what* happened in plain language, and *how to recover*. A generic "Something went wrong" with no next step is not an error state.

---

## Content & Attention Review Checklist

- [ ] Was a real **prioritization decision** made — something cut or demoted, not just shrunk?
- [ ] Does the screen have **one obvious primary focus**?
- [ ] Do the **headings alone** convey the gist (layer-cake scan)?
- [ ] Is important information **front-loaded** — top of screen, start of line?
- [ ] Is content **chunked** with clear groups and spacing, not a wall of text?
- [ ] Is emphasis **spent on meaning-carrying words**, not scattered?
- [ ] Is key **status glanceable** — readable by position/color/shape before text?
- [ ] Is there ever **more than one overlay** stacked? (Defect.)
- [ ] Does every overlay have an **obvious, non-gesture-only dismissal**?
- [ ] Are **empty / loading / error** states all designed, each with a way forward?
