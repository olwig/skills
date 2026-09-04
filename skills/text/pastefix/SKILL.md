---
name: pastefix
description: Clean pasted text without changing meaning or language unless a mode is named, while allowing small paste-glitch cleanup. Trigger on Paste Fix, paste-fix, pasted text cleanup, fix this paste, lowercase paste, formal paste, funny paste, short paste, or help for paste-fix modes. Default lowercases non-code/non-URL/non-quoted text and preserves structure. Modes are formal, funny, short, and help.
---

# Paste Fix

Take the user's pasted (or provided) text and return a cleaned version according to the active mode. Never explain the rewrite unless the user asked for Help or explicitly asked for a note.

## Core rules (all modes except Help)

- Keep the original language. Do not translate.
- Keep the meaning. Do not add facts, opinions, or missing context.
- Do not invent content that was not in the source.
- Do not ask follow-up questions about the paste unless the text is empty.
- Output only the resulting text. No preamble, no quotes around the whole result, no "here is the fixed version".
- Preserve structure when it matters (line breaks, lists, numbering) unless Short mode requires compression.
- If the user gives instructions plus a paste, treat everything after the mode keyword as the source text unless they clearly separate instruction from paste.
- If multiple modes are named, apply them in this order: Short (length) → Formal or Funny (tone) → Default casing rules only if Default applies. Formal and Funny do not stack; if both are named, Formal wins.
- If no mode is named, use Default.

## Default mode (no mode named)

This is the standard default behavior.

- Output non-code, non-URL, non-quoted text in lowercase (including the first letter of sentences and the pronoun I as i).
- Do not change wording beyond what is required to keep the text readable after lowercasing.
- Do not lengthen or shorten.
- Do not make it more formal or funnier.
- Keep punctuation and line breaks as in the source, only fixing accidental double spaces or broken wrap artifacts if they are clearly paste glitches (e.g. a hyphenated line-break mid-word may be rejoined). Do not "improve" style.

## Formal mode

Trigger words: formal, Formel, formell.

- Keep the original language.
- Raise register slightly: complete sentences where fragments were sloppy, standard spelling, restrained vocabulary.
- Do not add new ideas or extra sentences whose only job is to sound fancy.
- Keep length close to the source (same information density). A few extra function words are allowed if needed for grammar; do not inflate.
- Use normal sentence case (not all-lowercase).
- No jokes, slang, or emoji unless they were already in the source and removing them would change meaning.

## Funny mode

Trigger words: funny, funnier, jokey, joke.

- Keep the original language and the same facts.
- Lightly tint the text with dry humor, one short quip, or a playful aside — only where it does not bury the point.
- Do not turn the piece into a standup set. One or two light touches is enough.
- Do not lengthen much; humor must replace or sit next to existing lines, not add a second essay.
- Use normal sentence case (not all-lowercase).
- Stay kind. No punching down at groups. Sarcasm aimed at the situation in the text is fine.

## Short mode

Trigger words: short, kurz, shortest, as short as possible.

- Keep the original language.
- Compress to the shortest form that still carries the same claims, names, numbers, and constraints.
- Drop filler, repetition, and throat-clearing.
- Keep every concrete detail that would change a decision if omitted.
- Use normal sentence case unless Default is in effect per the Core rules precedence.
- Lists may become compact phrases. Do not drop list items.

## Help mode

Trigger words: help, hilfe, modes, modi.

When Help is requested, ignore any paste and explain the modes in the language of the help request; if that language is unclear, use English.

Explain exactly these modes:

1. Default — no extra mode named. Lowercase non-code, non-URL, non-quoted text only. Same language, same meaning, same length. Clean paste glitches only.
2. Formal — slightly more formal wording, same meaning, not padded.
3. Funny — same meaning with a light joke or playful tone, not a rewrite for laughs only.
4. Short — as short as possible while still factually complete.
5. Help — this explanation.

Mention that modes can be combined as Formal+Short or Funny+Short, that Formal beats Funny if both are named, and that Default casing follows the Core rules precedence (Short alone uses normal casing).

## Paste-glitch cleanup (all rewrite modes)

Allowed silent fixes:

- Rejoin words split by a line-wrap hyphen.
- Collapse 2+ spaces to one.
- Remove leftover markdown/quote artifacts the user clearly did not intend if the rest is plain text.
- Fix obvious keyboard-smash duplicates of a single character only when they are not stylistically intentional.

Forbidden:

- Changing names, numbers, dates, URLs, code, or quoted material content.
- "Fixing" dialect, profanity, or informal grammar in Default or Short unless Formal was named.
- Adding a title, hashtags, or emoji the source did not have (Funny may add at most one small playful mark if it fits).

## Empty or missing source

If there is no text to fix, reply with one line asking for the paste. Do not invent sample text unless Help was requested.
