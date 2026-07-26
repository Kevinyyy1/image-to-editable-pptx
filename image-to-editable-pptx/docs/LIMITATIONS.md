# Limitations and Fallback Policy

This project reconstructs an image into a practical editable PowerPoint. It cannot recover hidden source layers, original fonts, masks, vector paths, animation, or underlying data that are absent from the pixels.

## Native-editability boundary

| Input content | Expected result | Main limitation |
|---|---|---|
| Clear text | Native text box | Font metrics and wrapping can differ |
| Simple geometric shapes | Native PowerPoint shapes | Irregular edges may be approximated |
| Straight lines and basic arrows | Native shapes | Hand pressure and organic variation are lost |
| Simple hand-drawn underline or wave | Editable approximation | It will not reproduce every wobble |
| Complex brush, chalk, ink, or scribble work | Raster crop or simplified path | Faithful native conversion is usually impractical |
| Photos and people | Movable raster image | Internal pixels are not editable |
| Complex illustration and texture | Bounded raster image | Individual visual components are not separated |
| Table with readable structure | Native table or shapes | Merged cells and fine formatting may need review |
| Chart with recoverable values | Native chart | Missing data prevents true data editability |
| Chart without recoverable values | Editable shapes or raster | Values and series cannot be guaranteed |
| Text inside a photo or product mockup | Often raster | Perspective, occlusion, and lighting hinder extraction |

## Hand-drawn and organic marks

Hand-drawn visuals are especially difficult because their appearance depends on irregular pressure, texture, opacity, overlap, and noise. PowerPoint's native paths can approximate the geometry, but often cannot reproduce the material quality.

The workflow uses this fallback order:

1. Rebuild a simple line, arrow, underline, or wave as a native editable shape.
2. Approximate the mark with a small number of editable path points.
3. Keep a tightly cropped raster image when the mark materially affects the design.
4. Omit it only when it is decorative, cannot be separated cleanly, and omission produces less visual damage than a poor crop.

The output should disclose which fallback was used when a prominent element remains non-native.

## Text and OCR

OCR is not enabled by default because full-image OCR and repeated verification can substantially increase runtime and context use. Targeted OCR is appropriate for uncertain, small, or cropped regions.

Manual review is recommended for:

- small or blurred text;
- handwriting and decorative scripts;
- curved or rotated text;
- low-contrast text;
- text crossing photographs or textures;
- text inside phones, packaging, signs, clothing, or perspective mockups;
- punctuation, superscripts, subscripts, and unusual symbols.

## Fonts and rendering

The original font may not be available or identifiable. A substitute can change glyph width, line breaks, vertical alignment, and perceived weight. PowerPoint, LibreOffice, and different operating systems may render the same file differently.

For important deliveries:

- use licensed fonts available on the target system;
- inspect every slide in the intended presentation application;
- recheck line wrapping after font substitution;
- avoid relying on a single renderer as proof of fidelity.

## Collages, masks, and backgrounds

Dense editorial collages often contain overlapping paper, shadows, torn edges, tape, photos, and drawings. The source image does not expose clean layers beneath those objects. Reconstruction may therefore require:

- manually generated clean plates;
- approximate clipping masks;
- repeated z-order adjustments;
- cropped raster composites for inseparable regions;
- simplified shadows and torn edges.

## What “editable” means

Editable does not mean that every pixel becomes a separate object. The goal is useful editability:

- text can be rewritten;
- major shapes can be recolored and resized;
- photos can be moved, cropped, resized, or replaced;
- tables and recoverable charts can be edited;
- layers can be rearranged where they were reconstructed separately.

Raster fallbacks remain movable and replaceable, but their internal content is not editable.

## Quality expectations

The result should preserve the source's composition, hierarchy, and reading order while maximizing practical editability. Pixel-perfect reconstruction is not guaranteed. Very dense, low-resolution, distorted, or heavily textured inputs may require manual refinement after generation.

---

## 中文摘要

- 复杂手绘线条、笔刷涂抹、粉笔、墨迹、涂鸦和不规则撕纸边缘，通常无法完整转换成高保真的原生 PPT 路径。
- 简单箭头、下划线、波浪线可以用原生形状近似；复杂内容会采用局部图片、简化路径，或在纯装饰且无法分离时省略。
- 照片、人物、复杂插画和纹理会保留为可移动、可缩放、可替换的图片，但图片内部像素不可编辑。
- OCR 默认不进行全图扫描，以控制 token 和运行成本；小字、模糊字、手写字和图片内文字需要人工检查。
- 字体替换、换行和不同软件的渲染差异可能造成偏差。
- “可编辑”指关键文字、形状和结构可修改，并不代表每一个像素都会被拆成独立对象。
