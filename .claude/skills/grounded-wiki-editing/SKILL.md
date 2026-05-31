---
name: grounded-wiki-editing
description: Use when creating or editing articles in this repair wiki (content/**/*.md, Hugo/Docsy), expanding a stub, translating a page, or writing any encyclopedic content where you might pad sparse facts with plausible-sounding detail.
---

# Grounded Wiki Editing

## Overview

This wiki is an encyclopedia, not marketing and not your best guess. **Every factual claim must trace to one of two sources: (1) something the maintainer stated, or (2) something you researched and cited.** Nothing else goes on the page.

The maintainer is the domain authority. They know more than what is online. **Their word is final unless they explicitly ask you to research.** Do not "help" by adding what a symptom *could* be, what other causes *might* exist, or what steps *would* typically apply.

This mirrors Wikipedia's three core content policies: **Verifiability**, **No Original Research**, and **Neutral Point of View** ([core content policies](https://en.wikipedia.org/wiki/Wikipedia:Core_content_policies)).

## The Iron Rule

**If you cannot point to the exact maintainer statement or cited source for a sentence, delete the sentence.**

Write the two facts you were given. Stop. A short, true article beats a long, padded one.

## Forbidden Moves (these are hallucination)

You were given facts A and B. You must NOT add:

- **Symptom lists** the maintainer didn't give. "no chime, no fans, no display, no LED" — if they said "black screen," write *black screen*, nothing more.
- **Diagnostic procedures** (diode-mode readings, thermal camera, voltages, freeze spray) unless provided.
- **Step-by-step repair steps** beyond what was stated.
- **Numbers**: voltages, resistances, prices, percentages, timings, success rates.
- **Causal explanations** ("what's actually happening") invented to sound authoritative.
- **Frequency/severity claims**: "most common," "in the majority of cases," "usually," "almost always."
- **Reassurances**: "data is almost always intact," "routine repair."

If a section would be useful but you have no grounded content for it, **leave the section out or ask the maintainer** — do not fill it.

## Voice: encyclopedic, never self-promotion

This is a wiki, not a shop page. Remove and never add:
- "bring it to us," "we do this regularly," "book a slot," contact CTAs, ripperdoc.de links as advertising.
- First-person shop voice: "in the shop we see…," "many customers…," "we get reports…" → neutral: "the symptom appears as…," "there are reports that…".

Neutral guidance about *finding any competent shop* is fine; steering to a specific business is not.

## Handling gaps, uncertainty, and obsolete content

- **Gap** → omit or ask. Never bridge with a plausible guess.
- **Contradiction** → if a new instruction conflicts with text on the page, flag it and let the maintainer decide; don't silently reconcile by inventing a middle version. (e.g. "no longer needed" + "use an older IPSW" in one breath is a contradiction — surface it.)
- **Obsolete-but-useful** → keep as `~~strikethrough~~` reference under an "obsolete since X" heading, rather than deleting or contradicting it.
- **Researched claim** → only when the maintainer asks you to research. Then cite the source inline (prefer official/primary sources) and keep wording faithful to it.

## Project conventions

- **German terms** (DE pages): **Reparieren** = Revive, **Wiederherstellen** = Restore. Keep an English gloss in parentheses on first mention.
- **Bilingual**: changes to an article are mirrored in both `content/de/...` and `content/en/...`. DE URLs are `/wiki/docs/...`, EN are `/wiki/en/docs/...` (path lowercases "Mac repair" → "mac-repair").
- **Images**: page bundles (`slug/index.md` + image files alongside); self-host, reference bundle-relative. Hotlinks to logi.wiki/repair.wiki get 403 (Referer check).
- **Shortcodes**: Docsy `{{% alert title="…" color="info|warning" %}}…{{% /alert %}}` and `{{% pageinfo %}}` are available.
- **Verify before claiming done**: `hugo --quiet --gc` must exit 0; pages should serve 200 on the dev server.

## Red Flags — STOP, you are about to hallucinate

- You're writing a sentence you can't attribute to the maintainer or a cited source.
- You're adding a "Symptoms," "Diagnosis," or "What NOT to do" section the maintainer didn't supply content for.
- You typed "usually," "most," "often," "typically," "almost always" about something nobody told you.
- You're making the article "complete" or "useful" by filling gaps.
- You're adding a contact link or "we can help."

**All of these mean: delete it, or ask, or research-and-cite.**

## Rationalization table

| Excuse | Reality |
|--------|---------|
| "The article feels too thin." | Thin and true beats thick and fabricated. Ship the two facts. |
| "Readers need the full symptom list." | They need *correct* info. Invented symptoms are wrong info. |
| "This cause is technically plausible." | Plausible ≠ verified. The maintainer knows the real cause; you don't. |
| "I'm just being helpful / thorough." | Unrequested elaboration is the exact failure being prevented. |
| "Every wiki has a Diagnosis section." | Not with invented procedures. Omit what you weren't given. |
| "It's basically what a tech would do." | "Basically" = guessing. Don't put guesses in an encyclopedia. |
| "A bit of marketing won't hurt." | It's a wiki. Self-promotion is out of scope, full stop. |
| "The maintainer probably means X." | Don't assume. Ask. Their word is final. |
