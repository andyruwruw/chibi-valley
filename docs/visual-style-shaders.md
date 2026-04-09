# Visual Style & Shader Implementation

Reference for achieving the neo-retro / PS1 aesthetic in Unity URP.

---

## Chosen Approach

```
PS1 geometry effects  → Asset Store PS1 shader pack (vertex wobble, affine mapping)
Post-processing       → URP Volume system (bloom, god rays, color grading)
Pixelated textures    → Low-res textures (32x32, 64x64) + Point filtering (no mipmaps)
Color palette         → URP Global Volume color grading (bright, saturated, dream-pop)
```

---

## Shader Options

### Asset Store PS1 Shader Packs (Recommended)
**Best for:** Core PS1 aesthetic — vertex wobble, affine texture warping, color depth reduction  
**Recommended packs to evaluate:**
- **PSX Retro Shader** — most popular, well reviewed, URP compatible
- **Retro3D** — similar feature set, strong community
- **Low Poly Shader Pack** — less PS1-specific but good stylized base

**Strengths:**
- Vertex jitter/wobble out of the box
- Affine texture mapping (PS1's signature warping)
- Color depth reduction (limited color palette per PS1 hardware)
- Dithering support
- No HLSL knowledge required
- Days to implement vs. months from scratch

**Weaknesses:**
- Less control than custom shaders
- May not match exact aesthetic vision — requires configuration
- Cost
- Some packs are not actively maintained — verify URP/Unity 6 compatibility before purchase

**Pricing:** ~$20–$60 one-time  
**Where to look:** Unity Asset Store → search "PS1 shader URP" or "retro shader URP"  
**Decision trigger:** Buy before starting any visual work — apply globally early

---

### Custom URP Shaders (HLSL / Shader Graph)
**Best for:** Fine-tuned control when asset store packs don't cover a specific need  
**Strengths:**
- Full control
- Can be extended infinitely

**Weaknesses:**
- Requires HLSL or Shader Graph knowledge
- Time-consuming
- Not recommended as primary approach

**Pricing:** Free (your time)  
**Decision trigger:** Use only to fill gaps the purchased pack doesn't cover

---

### Flat Kit
**Best for:** Toon/cel-shading as an alternative stylized look  
**Strengths:**
- High quality, well maintained
- Strong stylized lighting control
- URP native

**Weaknesses:**
- Not PS1-specific — different aesthetic
- Vertex wobble not included

**Pricing:** ~$50 one-time  
**Decision trigger:** Not primary — consider only if PS1 packs prove incompatible

---

## URP Post-Processing Stack

All applied via URP **Global Volume** component:

| Effect | URP Tool | Purpose |
|--------|----------|---------|
| Bloom / soft glow | Bloom (built-in) | Dream-pop atmosphere |
| God rays / sun shafts | Screen Space Lens Flare or custom Renderer Feature | Outdoor scenes |
| Color grading | Tonemapping + Color Adjustments | Bright, saturated, high contrast |
| Vignette | Vignette (built-in) | Subtle framing |
| Pixelation | Custom Renderer Feature or shader | Reduce effective resolution |
| Dithering | PS1 shader pack | Color depth reduction |

---

## Texture Guidelines

To achieve the pixelated low-res look:

- **Texture resolution:** 32x32 or 64x64 for most surfaces, 128x128 max for hero assets
- **Filter mode:** Point (no filter) — never Bilinear or Trilinear
- **Mipmaps:** Disabled
- **Compression:** None or lossless — compression artifacts fight the pixel art aesthetic
- **Pixel art creation:** Aseprite (pixel art editor) or Leonardo.ai / Stable Diffusion with pixel art LoRA

---

## Color Palette Reference

Target: "dream-pop" / "cottagecore" — bright, saturated, high contrast

- Greens: warm sage and lime, not cool/grey
- Browns: warm terracotta, not muddy
- Sky: powder blue to golden hour orange
- Avoid: desaturated, muted, or gritty tones

Apply globally via URP Color Adjustments:
- Saturation: +20 to +40
- Contrast: +10 to +20
- Color Filter: slight warm tint (cream/yellow)

---

*Last updated: 2026-04-09 — verify asset store pack URP/Unity 6 compatibility before purchase*
