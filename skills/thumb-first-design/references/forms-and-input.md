# Forms & Input

Designing and reviewing any screen that asks the user to enter data. Lens: **Focus Forcer (Wroblewski)**, supported by **Direct Manipulation (Clark)** and **Platform Canon**.

Load this for sign-up, checkout, settings, search, profile editing, or any data-entry screen.

---

## The Core Position

Wroblewski's *Web Form Design* and *Mobile First* remain the definitive source. The central idea: **a form is a cost you ask the user to pay.** Mobile makes that cost visible — every field is a tap, a keyboard, a moment of friction on a small screen. Design the form by *removing* cost, not by arranging fields.

Modern note (Budiu / NN/g): authentication friction has genuinely dropped — passkeys, biometrics, autofill, magic links. Forms are no longer the #1 mobile pain point they were in 2011. But *that's because* these principles got adopted — they didn't stop mattering, they became table stakes.

---

## Structural Rules (durable, non-negotiable on mobile)

1. **Single column. Always.** No side-by-side fields on a phone — not even "City" and "ZIP." Multi-column forms break the top-to-bottom scan and the keyboard's natural advance. The only exception is genuinely paired micro-inputs that are *one* value (e.g., a card-expiry MM / YY), and even those are often better as one field.

2. **Labels stay visible — top-aligned.** The label sits *above* the field. Eye-tracking shows top-aligned labels let the eye capture label and field in one fixation, and — critically on mobile — they stay visible when the keyboard pushes the layout up.

3. **Placeholder-as-label is an anti-pattern.** A label inside the field disappears the moment the user starts typing — exactly when they need to confirm what the field is. Placeholders are for *examples* ("e.g. jane@work.com"), not for the label itself. (The "floating label" pattern — placeholder that animates up into a label on focus — is an acceptable compromise; a static placeholder-only field is not.)

4. **Match the input type to the data.** Every field declares its type so the right keyboard appears: email → email keyboard, phone → number pad, URL, numeric, decimal. A wrong keyboard is an avoidable tax paid on every entry. Pair with autocomplete/autofill hints (name, email, one-time-code, address) and the right capitalization and return-key label.

5. **Reduce fields aggressively.** For every field ask: *"What happens if we remove this?"* If the honest answer is "nothing breaks," remove it. Wroblewski's Boingo case collapsed a five-screen form to one screen this way. Ask later, ask progressively, infer from context, or don't ask at all.

6. **Use smart, adjustable defaults.** A pre-filled-but-editable value (detected country, today's date, the obvious option) is faster than an empty field — measurably so. Defaults are a gift only if the user can override them easily.

---

## Interaction & Feedback

- **Validate inline, at the right time.** Validate a field when the user *leaves* it (not on every keystroke, not only on submit). Show success and error at the field, in plain language, with how to fix it.
- **Never clear the form on error.** Preserve every entered value. Re-typing a form because one field failed is a top abandonment cause.
- **Keep the active field and its label visible above the keyboard.** When the keyboard opens it eats ~40% of the screen. The focused field, its label, and its inline error must remain in the visible band — scroll/inset the layout so they do.
- **Put the submit action where the thumb is and where the eye ends.** A primary submit button at the bottom of the single column, in the Natural reach zone. For long forms, a persistent bottom-anchored submit can work — but don't let it cover the field being edited.
- **Show progress for multi-step forms.** If the form is a wizard, the user must always know where they are and how much is left.

---

## Choosing the Input Control

Match the control to the data, and prefer the lowest-friction one that fits:

| Data | Prefer | Avoid |
|------|--------|-------|
| One choice, 2-4 options | segmented control or visible radio set | dropdown (hides options, extra taps) |
| One choice, 5-15 options | native picker / select | long radio list |
| One choice, many options | searchable list screen | giant scroll picker |
| Multiple choice | visible checkboxes / toggle chips | multi-select dropdown |
| On/off | switch | checkbox for a setting |
| Date | native date picker | three separate number fields |
| Free text | sized to the expected input | a tiny box for a long answer |

**Clark's angle:** prefer manipulating a value directly over wrapping a control around it. A stepper you tap, a slider you drag, a chip you toggle — these are often better than a text field plus validation. But don't sacrifice precision: a slider is wrong for a value that needs an exact number.

---

## Forms Review Checklist

- [ ] **Single column** throughout?
- [ ] Labels **visible and top-aligned** (no placeholder-as-label)?
- [ ] Every field declares the **correct input type / keyboard** and autofill hint?
- [ ] Has every field passed the **"what if we removed it?"** test?
- [ ] Are there **smart, editable defaults** where a value can be inferred?
- [ ] Does the focused field + label + error stay **visible above the keyboard**?
- [ ] Is validation **inline and on-blur**, in plain language, with a fix?
- [ ] Is entered data **preserved on error**?
- [ ] Is the submit action **bottom-anchored** in thumb reach?
- [ ] For multi-step: is **progress** always visible?
- [ ] Is each input **control** the lowest-friction one that fits the data?
