# Image to Editable PPTX

A token-conscious Codex/ChatGPT skill for reconstructing screenshots, slide images, PDF pages, and image-only presentations as editable PowerPoint files.

It converts readable text, cards, simple diagrams, tables, charts, lines, arrows, and badges into native PowerPoint objects. Photos, textures, people, and complex artwork are retained as bounded movable images when native reconstruction would reduce fidelity.

## What it does

- Rebuilds image-based slides as editable `.pptx` files.
- Preserves the original aspect ratio and visual hierarchy.
- Uses native text boxes and PowerPoint shapes where practical.
- Keeps complex raster content cropped and independently movable.
- Supports low-token reconstruction with optional targeted OCR.
- Includes scene linting, editability auditing, rendering, and visual comparison scripts.
- Offers `standard` and `strict` QA modes.

## Important limitations

This tool reconstructs a slide; it does not recover the original design source.

- Hand-drawn lines, brush strokes, chalk marks, ink scribbles, rough highlights, and irregular torn-paper edges may not convert faithfully into native editable PowerPoint paths.
- Simple arrows, underlines, and waves can usually be approximated. Complex freehand artwork may be simplified, kept as a raster crop, or omitted when it is purely decorative and cannot be separated cleanly.
- Photos, people, detailed illustrations, paper textures, shadows, and other complex artwork remain raster images. They are movable, resizable, replaceable, and croppable, but their internal pixels are not editable.
- Text embedded in packaging, clothing, photos, mockup screens, or complex artwork may remain raster or require manual cleanup.
- Font substitutions, line wrapping, and rendering differences can occur across PowerPoint, LibreOffice, operating systems, and installed font sets.
- Charts are fully data-editable only when the underlying data can be recovered. Otherwise, they may be rebuilt as editable shapes.
- Small, blurred, stylized, curved, or low-contrast text may require manual review. OCR is intentionally optional to reduce token and runtime cost.
- Dense collages often require manual masking, z-order work, and clean background reconstruction.
- Pixel-perfect equivalence is not guaranteed.

See [docs/LIMITATIONS.md](docs/LIMITATIONS.md) for the full boundary and recommended fallbacks.

## Repository structure

```text
image-to-editable-pptx/
├── README.md
├── LICENSE
├── NOTICE
├── requirements.txt
├── CONTRIBUTING.md
├── SECURITY.md
├── docs/
│   └── LIMITATIONS.md
├── examples/
│   └── README.md
└── skill/
    └── image-to-editable-pptx/
        ├── SKILL.md
        ├── agents/
        ├── references/
        └── scripts/
```

The installable skill is only the `skill/image-to-editable-pptx/` directory. Repository documentation stays outside that directory so it does not inflate the skill context.

## Requirements

This repository is designed for a Codex/ChatGPT environment that provides the built-in Presentations workflow and `@oai/artifact-tool`.

Python dependencies:

```bash
python -m pip install -r requirements.txt
```

Additional runtime tools used by the full workflow:

- Node.js
- LibreOffice (`soffice`) for slide rendering
- Poppler for PDF rendering
- Tesseract OCR, optional
- Fontconfig, recommended

## Install the skill

1. Download or clone this repository.
2. Locate `skill/image-to-editable-pptx/`.
3. Upload or install that folder as a skill. Its root must contain `SKILL.md`.
4. Start a new request such as:

> Use image-to-editable-pptx to reconstruct this screenshot as a high-fidelity editable PowerPoint. Keep photos as bounded images, convert readable text and simple shapes to native objects, and use standard QA.

Do not upload the repository root as the skill itself unless the destination supports selecting the nested skill folder.

## Optional local pipeline

From the repository root:

```bash
python skill/image-to-editable-pptx/scripts/pipeline.py \
  --scene run/scene.json \
  --output run/reconstruction.pptx \
  --run-dir run/pipeline \
  --mode standard \
  --crop-failures
```

Use `strict` mode when geometry, editable-text coverage, or render similarity needs tighter enforcement:

```bash
python skill/image-to-editable-pptx/scripts/pipeline.py \
  --scene run/scene.json \
  --output run/reconstruction.pptx \
  --run-dir run/pipeline \
  --mode strict \
  --crop-failures
```

## Editability model

| Source element | Typical output |
|---|---|
| Readable titles and body text | Native text boxes |
| Cards, labels, badges, dividers | Native PowerPoint shapes |
| Simple arrows, lines, and diagrams | Native shapes or editable paths |
| Tables and recoverable charts | Native editable objects |
| Photos and people | Cropped movable images |
| Textures and complex illustrations | Bounded raster images |
| Complex hand-drawn marks | Approximation, raster fallback, or omission |

## 中文说明

这是一个把截图、图片式幻灯片、PDF 页面或图片型 PPT 重建为可编辑 PowerPoint 的 Skill。它优先把清晰文字、卡片、表格、基础图表、线条和简单图形转为原生 PPT 对象；人物、照片、纹理和复杂插画保留为可移动、可缩放、可替换的图片。

需要特别说明：复杂手绘线条、笔刷涂抹、粉笔字、墨迹、撕纸边缘和不规则涂鸦通常无法完全转成高保真的原生可编辑路径。简单箭头、波浪线和下划线可以近似重建；复杂装饰可能被简化、保留为局部图片，或在无法干净分离时省略。

## Privacy and content rights

Input images and presentations may contain confidential information. Review your environment and data-handling requirements before processing sensitive material. Only use images, fonts, logos, and other content that you have permission to use.

## License

Original repository content is released under the [MIT License](LICENSE). Third-party tools and dependencies remain subject to their own licenses; see [NOTICE](NOTICE).
