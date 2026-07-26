# Contributing

Contributions that improve reconstruction quality, editability, token efficiency, or portability are welcome.

## Guidelines

- Keep `skill/image-to-editable-pptx/SKILL.md` concise and action-oriented.
- Put detailed operational knowledge in `references/`, not in the main skill file.
- Do not add repository documentation such as `README.md` inside the installable skill folder.
- Prefer deterministic scripts for repeated or fragile operations.
- Preserve the low-token default: OCR and expensive visual checks should remain targeted or opt-in.
- Document any change that affects output editability, runtime requirements, or QA thresholds.
- Do not commit confidential decks, copyrighted test images without permission, generated presentations, or large binary fixtures.

## Basic validation

Run these checks before opening a pull request:

```bash
python -m py_compile skill/image-to-editable-pptx/scripts/*.py
node --check skill/image-to-editable-pptx/scripts/render_pptx.mjs
node --check skill/image-to-editable-pptx/scripts/scene_to_pptx.mjs
```

If you change the scene format, update both `references/scene-schema.md` and the relevant lint/build scripts.

## Pull requests

Describe:

- the reconstruction problem being solved;
- the expected effect on fidelity and editability;
- the expected effect on token and runtime cost;
- the tests or example inputs used;
- any known limitations or backward-compatibility concerns.
