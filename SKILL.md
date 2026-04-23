---
name: scene-director
description: Plans cinematic images by filling a 12-field director sheet (subject, action, environment, shot, camera, lens, lighting, composition, color grade, mood, aspect ratio, anti-list) that compiles into a model-optimized prompt. Use when generating or iterating on images with GPT Image 2, OpenArt, Midjourney, Flux, Nano Banana, Ideogram, or any text-to-image model, or when the user mentions cinematic shots, hero images, portraits, product photos, album covers, concept art, editorial imagery, or gives a short prompt where creative intent is richer than the text typed.
---

# Scene Director

## Overview

**One-line principle:** Think like a film director, not a typist. Plan the shot before you render it.

A short creative prompt ("a woman drinking coffee at sunrise") is ambiguous in twelve ways — lens, framing, subject identity, emotional beat, light direction, location specificity, wardrobe, color grade, era, medium, mood, aspect ratio. Image models silently pick one combination; creators usually meant another. The result is retries.

Scene Director forces an explicit reasoning pass: extract intent → fill a structured director sheet → compile the sheet into a model-optimized prompt → generate → critique → revise the sheet (not the prompt).

This skill pairs especially well with GPT Image 2, whose reasoning layer can act on structured input. It also works with any diffusion model — the sheet compiles differently per target.

## When to Use

Use this skill when:

- The user gives a short or ambiguous image prompt (fewer than ~25 words, or no explicit framing/lighting/camera cues).
- The user says the word "cinematic", "hero shot", "editorial", "poster", "keyframe", "concept art", "product photo", "portrait", "album cover", or similar loaded terms.
- The user is iterating ("make it more dramatic", "warmer", "cleaner") — the sheet is what you edit, not the raw prompt.
- The user names a target model (GPT Image 2, OpenArt, Midjourney, Flux, Nano Banana, Veo, Sora) — compile the sheet for that model.
- The user uploads a reference image and asks for a variant — extract the sheet *from* the reference first, then modify.

Do NOT use this skill when:

- The user already provided a fully-specified prompt and just wants it passed through.
- The task is pure text rendering (logos, typography samples) where narrative framing is irrelevant.
- The user explicitly says "no planning, just generate."

## Core Workflow

```
User intent (short prompt)
      ↓
[1] Interrogate — ask at most 2 clarifying questions ONLY when intent is load-bearing ambiguous
      ↓
[2] Fill the Director Sheet (12 fields, see Quick Reference)
      ↓
[3] Compile — render sheet into a model-specific prompt string (see references/gpt-image-2-prompting.md)
      ↓
[4] Generate — call the image model
      ↓
[5] Critique — compare output to sheet. What's off?
      ↓
[6] Revise the SHEET (not the prompt). Re-compile. Re-generate.
```

The key move is step 6. Editing the sheet keeps intent traceable across iterations; editing raw prompt strings loses the plot.

## Quick Reference: The Director Sheet (12 Fields)

| # | Field | Question it answers | Example |
|---|-------|---------------------|---------|
| 1 | **Logline** | One sentence, what is this image? | "A tired founder reads a term sheet at 2am." |
| 2 | **Subject** | Who/what, specifically? Age, build, wardrobe, ethnicity if relevant. | "Woman, 30s, South Asian, hoodie over pajamas, hair up" |
| 3 | **Action / beat** | What exact moment? | "Mid-sigh, pen held mid-air, eyes fixed on paper" |
| 4 | **Environment** | Where, and what tells us that? | "Home office, IKEA desk, plant, window showing city lights" |
| 5 | **Time & era** | When, literally. | "2am, present day (2026)" |
| 6 | **Shot type** | Wide / medium / close-up / extreme close-up / OTS | "Medium close-up" |
| 7 | **Camera & lens** | Focal length, angle, height | "35mm, eye-level, slight Dutch tilt" |
| 8 | **Lighting** | Key + fill + practical sources, direction, quality | "Warm desk lamp key from camera-right, cool window fill from behind, practical laptop glow on face" |
| 9 | **Composition** | Rule of thirds / centered / leading lines / negative space | "Subject on left third, paper fills bottom-right, negative space top-left for title" |
| 10 | **Color & grade** | Palette, contrast, film stock | "Teal shadows, amber highlights, Kodak Portra 400 feel, gentle grain" |
| 11 | **Mood** | Emotional tone in 2-3 words | "Weary, hopeful, private" |
| 12 | **Deliverable** | Aspect ratio, output size, use case | "3:2 landscape, hero image for pitch deck cover slide" |

An optional 13th field — **Anti-list**: things to explicitly exclude ("no stock-photo smile, no plastic skin, no generic office"). For GPT Image 2 this maps cleanly to negative guidance.

## Core Pattern: Intent → Sheet → Prompt

**Before** (raw prompt, typical user input):
```
cinematic shot of a founder reading term sheet at 2am, moody
```

**After** (director sheet compiled for GPT Image 2):
```
A medium close-up of a South Asian woman in her 30s, wearing a soft grey hoodie
over pajamas, hair loosely tied up. She is mid-sigh, holding a pen in her right
hand just above a paper term sheet on a cluttered IKEA desk. Eyes locked on the
document. Home office at 2am, plants, a window behind her revealing out-of-focus
city lights. Shot on a 35mm lens, eye-level, very subtle Dutch tilt. Key light
is a warm tungsten desk lamp from camera-right; cool blue fill spills from the
window behind; a practical laptop glow lifts her face from below. Subject sits
on the left third; paper fills the bottom-right; negative space top-left reserved
for title treatment. Teal shadows, amber highlights, Kodak Portra 400 color
response, gentle natural film grain. Mood: weary, hopeful, private. Photoreal,
3:2 landscape. Avoid: stock-photo smile, plastic skin, generic corporate office,
over-sharp digital look.
```

Same creative seed. Ten times more faithful to intent. No additional user effort — the skill did the work.

See `references/gpt-image-2-prompting.md` for the full compilation ruleset, and `assets/director-sheet-template.md` for a fillable template.

## Keep the Sheet, Not the Prompt

When iterating, ALWAYS edit fields on the sheet, not tokens in the prompt. Rationale: prompts are lossy projections of intent; sheets are the source of truth. A user saying "warmer" should update field 10 (color & grade) and field 8 (lighting temperature), after which the prompt recompiles automatically. This is how you get predictable iteration at 8M-MAU scale — the sheet is diffable, the prompt is not.

## Quality Bar (Anti-Slop Rules)

These rules exist because default model output trends toward a "plastic" look. Enforce them in the compile step unless the user's logline explicitly calls for a stylized/illustrative result.

1. Name a specific medium or film stock (35mm film, Portra 400, Fuji Pro 400H, medium-format digital, etc.). Never just "photorealistic".
2. Specify light *direction and quality*, not just "moody lighting". Directors speak in key/fill/practical/rim/bounce.
3. Prefer a real focal length (24mm, 35mm, 50mm, 85mm, 135mm) over "cinematic lens".
4. Include one imperfection (grain, skin texture, slight motion blur, dust in the light beam) — this is how you escape CGI gloss.
5. If humans are in the shot, give them a specific *beat*, not a generic action. "Mid-sigh" beats "sitting thoughtfully."
6. Anti-list at least 3 things the model reliably over-generates (plastic skin, symmetrical stock-photo grin, floating hands, generic interior, HDR blown highlights).

## Handling Iteration

User says "make it moodier" → don't just add the word "moody". Walk the sheet:

- Field 8 (lighting): reduce fill, harden key, add practical shadow
- Field 10 (grade): deeper shadows, desaturate mid-tones
- Field 11 (mood): update language
- Field 3 (action): consider a more contemplative beat

Then recompile. The difference between amateur and professional image direction is *which field* you edit.

## Handling Reference Images

If a reference image is provided, invert the workflow: extract the sheet FROM the reference (what shot type, what lens, what grade is this?), then have the user modify specific fields. This prevents the common failure mode of "make it look like this but different" where the model keeps no anchor.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Asking the user 10 clarifying questions | Ask at most 2; fill the rest with sensible defaults and flag them explicitly in the sheet |
| Over-specifying (every field padded with adjectives) | Minimum viable sheet; leave fields terse when intent is clear |
| Describing the *image* instead of the *moment* | Describe action as a verb in a beat, not a pose |
| Forgetting the anti-list | GPT Image 2 benefits enormously from explicit negative guidance |
| Editing the compiled prompt instead of the sheet | Treat the prompt as a build artifact; never hand-edit |
| Pretending to know the subject's identity | For fictional people, describe visually; never invent a real named person |

## Integration Targets

- **GPT Image 2 / ChatGPT Images 2.0**: full structured prompt, the reasoning layer benefits most from explicit sheets.
- **OpenArt (Smart Shot, Worlds, Characters)**: the sheet is the perfect upstream artifact — compile into whichever of OpenArt's canvases the user is working in.
- **Midjourney**: compile with `--ar`, `--stylize`, `--chaos` at the tail.
- **Flux / Nano Banana / Ideogram**: compile with emphasis on composition and typography fields.
- **Veo 3.1 / Sora 2**: add field 3.b (camera *move*: dolly-in, rack focus, handheld) for video.

See `references/` for per-model compile rules.

## Files in This Skill

- `SKILL.md` — this file
- `references/director-sheet-schema.md` — the full JSON schema for programmatic use
- `references/cinematic-vocabulary.md` — shot types, lens characteristics, lighting terms, film stocks
- `references/gpt-image-2-prompting.md` — model-specific compile rules and examples
- `assets/director-sheet-template.md` — fillable markdown template for humans
- `scripts/render_prompt.py` — converts a director-sheet JSON into a compiled prompt string
- `evals/evals.json` — three realistic test prompts for red/green testing

## The Bottom Line

Image models don't fail at rendering. They fail at *guessing what you meant*. Scene Director closes that gap with a sheet, a compile step, and an iteration protocol that edits intent rather than text.

For OpenArt's 8M MAU, the value is mechanical: every retry that becomes a sheet-edit is one less generation spent chasing a feeling the model couldn't read from a 12-word prompt.
