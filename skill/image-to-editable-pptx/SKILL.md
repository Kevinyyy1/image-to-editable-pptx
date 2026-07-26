---
name: image-to-editable-pptx
description: Reconstruct PNG, JPG, screenshot, rendered slide, PDF page, or image-only PPTX inputs as high-fidelity editable PowerPoint decks while controlling context and token use. Use when readable text, charts, tables, cards, lines, arrows, badges, and simple diagrams must become native editable PPT objects; keep only photos, textures, characters, and complex artwork as bounded movable pictures.
---

# Image to Editable PPTX

Reconstruct source images through a compact scene contract, deterministic
Artifact Tool compilation, and script-first QA. Preserve visual fidelity without
feeding raw OCR, full inspection logs, or complete QA traces back into context.

## Required Companion

Read and follow the built-in `Presentations` skill. Use
`@oai/artifact-tool`; never use `python-pptx`.

Read these references:

- Always read [element-routing.md](references/element-routing.md) and
  [qa-contract.md](references/qa-contract.md).
- Read [layer-decomposition.md](references/layer-decomposition.md) when building
  the inventory or clean plate.
- Read [font-policy.md](references/font-policy.md) for Chinese, Japanese, Korean,
  mixed-language, or decorative-font slides.
- Read [scene-schema.md](references/scene-schema.md) only when authoring or
  changing `scene.json`.

## Token Budget Rules

- Inspect the source at full size once; revisit only failed regions.
- Do not paste raw OCR output, full OOXML inspection, or full QA reports into
  context when a compact summary is sufficient.
- Keep OCR off by default. Use local OCR only for unreadable small text, dense
  numbers, or a user-requested strict review.
- Run deterministic scripts as black boxes. Read `qa-summary.json`, not
  `qa-report.json`, unless diagnosing a failed check.
- When a region fails, inspect its source/render crops rather than the whole
  slide.
- Limit automatic reconstruction repair to two complete passes in `standard`
  mode and three in `strict` mode.

## Output Contract

- Rebuild every reviewed word, number, formula, caption, and label as native
  PowerPoint text.
- Rebuild cards, panels, rules, arrows, charts, tables, and simple icons as
  native objects when practical.
- Keep photos, characters, paintings, textures, and dense illustrations as
  bounded local pictures.
- Use one reviewed textless clean plate as the only permitted full-slide image.
- Never put editable text over the same readable text inside a picture.
- Never use a text-bearing full-slide screenshot as a shortcut.
- Preserve source composition. Do not redesign the slide into a new template.

## Modes

Use `standard` unless the user explicitly requests maximum inspection.

### Standard

- Visually transcribe clear text.
- Keep OCR off unless a small region is ambiguous.
- Run structural, overflow, global, critical-region, and clean-plate QA.
- Print only the compact QA summary.
- Perform at most two full repair passes.

### Strict

- Use local OCR selectively for small or ambiguous regions.
- Use stronger similarity thresholds.
- Check more critical regions and crop every failed region.
- Perform at most three repair passes.

Strict mode increases compute and inspection, not permission to load unfiltered
logs into context.

## Workflow

### 1. Preflight

```bash
python scripts/preflight.py INPUT... \
  --json-out RUN_DIR/preflight.json \
  --fontconfig-file FONTCONFIG_FILE
```

Stop when the input, Artifact Tool, renderer, or required fonts are unavailable.
A missing OCR language is not a blocker when the text is visually reviewable.

### 2. Inventory Once

Inspect the page at full resolution and inventory:

- baseboard;
- structural surfaces;
- complex visual assets;
- native text;
- front decorations.

Create one record for each visible semantic item. Group only genuinely
inseparable decorative marks. Use tight bounds; a full-slide decoration group
destroys clean-plate mask coverage.

### 3. Build the Clean Plate

Use a native fill, source-preserving inpainting, or source composite. Keep it
textless and free of detachable pictures.

For dense collages, prefer one of:

- `clean_plate.reference_image`: compare the rendered baseboard against a
  reviewed clean plate over the full canvas;
- `clean_plate.mask_image`: use a precise white-ignore mask instead of the union
  of coarse rectangles.

Do not lower coverage thresholds to hide imprecise masks.

### 4. Author a Compact Scene

Use scene version `1.2` when `defaults`, `styles`, or `style_ref` reduce repeated
fields. Version `1.1` remains supported. The build step expands both formats to
the same deterministic compiler input.

Keep visible text in `expected_text`. Normalize text to Unicode NFC. Store JSON
as UTF-8.

### 5. Prepare Bounded Visual Assets

Prefer, in order:

1. exact source crop without semantic text;
2. source-preserving local cleanup;
3. regenerated textless asset when cleanup is not reliable.

Declare `contains_readable_text: false` and a concise `complex_reason`.
Generated assets must contain no words, letters, numbers, logos, labels,
signatures, seals, or watermarks.

### 6. Build and Audit

Preferred compact command:

```bash
python scripts/pipeline.py \
  --scene RUN_DIR/scene.json \
  --output RUN_DIR/reconstruction.pptx \
  --run-dir RUN_DIR/pipeline \
  --mode standard \
  --fontconfig-file FONTCONFIG_FILE \
  --crop-failures
```

Use `--mode strict` only when needed.

The pipeline writes:

- `reconstruction.pptx`;
- `qa/editability-report.json`;
- `qa/qa-report.json`;
- `qa/qa-summary.json`;
- failed-region crops when requested.

Read `qa-summary.json` first. Open the full report only for a named failed check.

### 7. Repair Only Failed Evidence

Fix:

- missing or duplicated text;
- clipping, wrapping, tofu, or mojibake;
- wrong z-order or crop;
- stretched circles or pictures;
- source text remaining inside pictures;
- failed critical regions;
- imprecise clean-plate masks.

Rebuild after each repair. Stop after the mode's pass limit and report unresolved
limitations rather than entering an unbounded loop.

### 8. Deliver

Deliver the editable `.pptx`, `editability-report.json`, and `qa-report.json`.
State which bounded complex visuals remain pictures. Do not describe picture
pixels as editable artwork.

## Hard Failures

Fail delivery when:

- reviewed text is missing from native PPT text;
- a required inventory item is unmapped;
- the clean plate contains semantic text or detachable visuals;
- a text-bearing picture covers most of the slide;
- an undeclared full-slide picture exists;
- a semantic chart or table is flattened without a stated limitation;
- the PPTX contains zero-byte media or opens with a repair warning;
- text clips, a one-line title wraps, or a circle becomes an oval;
- any declared critical region fails;
- the structural audit disagrees with the scene contract.
