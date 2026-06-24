---
name: plan-file-preview
description: Requires a pre-implementation file impact summary before executing a feature from a plan file. Use when the user asks to implement, execute, start, or work from @plans/<plan-name>.md, plans/*.md, or any named implementation plan.
---

# Plan File Preview

Before implementing any feature or task from a plan file, pause and produce a short file impact preview.

## Trigger

Use this skill when the user asks to implement, execute, start, continue, or work on something from:

- `@plans/<plan-name>.md`
- `plans/<plan-name>.md`
- any markdown implementation plan that represents planned feature work

Examples:

- "Zaimplementuj @plans/auth.md"
- "Startujemy z plans/payment-flow.md"
- "Wykonaj plan z @plans/dashboard.md"

## Required behavior

1. Read the referenced plan file first.
2. Do not start editing code yet.
3. Write a concise preview containing:
   - which files will likely be changed or created,
   - why each file needs to change,
   - any files that need investigation before deciding,
   - any uncertainty.
4. Ask the user for confirmation before implementation unless the user explicitly said to proceed without confirmation.

## Output format

```md
## Przed implementacją

Plan: `@plans/example.md`

### Pliki do zmiany / utworzenia

- `src/path/file.ts` — dlaczego ten plik trzeba zmienić.
- `src/path/NewComponent.svelte` — dlaczego trzeba go utworzyć.

### Do sprawdzenia przed zmianą

- `src/other/file.ts` — czego nie jestem jeszcze pewien.

### Ryzyka / uwagi

- Krótka lista zależności albo założeń.

Czy mogę przejść do implementacji?
```

Keep the preview practical and specific. If the plan is vague, say what is unclear and ask targeted questions before implementing.
