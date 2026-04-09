# Level Design & World Building Tools

Reference for world building tools and approaches. Decision made: Synty asset packs + Unity Tilemap + ProBuilder as primary workflow.

---

## Chosen Approach

```
Farm areas     → Unity Tilemap (grid-based, crop/building placement)
World blocking → ProBuilder (fast geometry prototyping in-editor)
Environment    → Synty asset packs (modular low-poly pieces, snap together)
```

---

## Tool Options

### Unity Tilemap (Built-in)
**Best for:** Farm grid — soil tiles, path tiles, water tiles  
**Strengths:**
- Built into Unity, no extra cost
- Grid-snapping built in — perfect for farming mechanics
- Each tile can store data (crop type, growth stage, watered state)
- Well-documented, many Stardew-style tutorials target this

**Weaknesses:**
- Editor UX is clunky for large maps
- 3D tilemap (isometric) less mature than 2D tilemap
- Not great for organic/natural areas

**Pricing:** Free (Unity built-in)  
**Decision trigger:** Use for all farm/homestead grid areas

---

### ProBuilder (Unity Package)
**Best for:** Blocking out level geometry, custom structures, terrain shapes  
**Strengths:**
- Works directly inside Unity editor — no round-tripping to Blender
- Great for rapid prototyping of building interiors, caves, custom terrain
- Free Unity package
- Good for "grey-boxing" levels before art pass

**Weaknesses:**
- Not a full modelling tool — limited for complex organic shapes
- Output is not optimized mesh — needs cleanup before final use
- Not a replacement for proper assets

**Pricing:** Free (Unity package)  
**Decision trigger:** Use for custom geometry that Synty packs don't cover, and for early level prototyping

---

### Synty Studios Asset Packs
**Best for:** Environment art — the entire explorable world  
**Strengths:**
- Industry-standard low-poly modular environment kits
- Art style matches the target aesthetic extremely closely
- Modular pieces snap together cleanly in Unity
- Massive time saver vs. generating environments via AI
- Consistent style across all packs
- Active updates and Unity support
- URP compatible

**Weaknesses:**
- Cost (one-time per pack, but adds up)
- Not unique — other games use same packs (mitigate with custom shaders/palette)
- Some pieces may need shader adjustments for PS1/vertex wobble aesthetic
- You're dependent on their asset style decisions

**Pricing:** ~$60–$150 per pack (one-time). Often on sale. Synty also offers a subscription (~$14.99/mo for full library access).  
**Recommended packs:**
- POLYGON Farm — farm buildings, crops, animals, tools
- POLYGON Nature — trees, rocks, foliage, terrain pieces
- POLYGON Adventure — dungeons, caves, ruins, secret areas
- POLYGON Town / Village — NPC buildings, market stalls
- POLYGON Icons — UI/inventory icons (pixel art style)

**URL:** https://syntystore.com  
**Decision trigger:** Primary source for all environment and world art

---

### Tiled + SuperTiled2Unity
**Best for:** Complex 2D/isometric map authoring with external editor  
**Strengths:**
- Better map editor UX than Unity's built-in Tilemap
- Supports object layers, custom properties per tile
- Free and open source

**Weaknesses:**
- Extra pipeline step (external tool → import)
- Primarily 2D focused — 3D isometric support is limited
- More setup overhead

**Pricing:** Free  
**Decision trigger:** Not recommended for this project given 3D nature — revisit only if Unity Tilemap proves too limiting

---

### Hand-Placed Prefabs
**Best for:** Unique one-off props, hero assets, special locations  
**Strengths:**
- Maximum flexibility
- Every asset exactly where you want it

**Weaknesses:**
- Very slow at scale
- Hard to maintain consistency

**Pricing:** Free  
**Decision trigger:** Use only for hero moments (boss rooms, special NPC locations, puzzle set pieces) — not for general world filling

---

## World Structure Decision

| Area Type | Grid? | Primary Tool | Art Source |
|-----------|-------|-------------|------------|
| Farm/homestead | Yes (tile grid) | Unity Tilemap | Synty POLYGON Farm |
| Town/village | Loose grid | Synty prefabs + hand placement | Synty Town/Village |
| Nature/exploration | No | Synty prefabs + ProBuilder terrain | Synty Nature/Adventure |
| Dungeon/caves | No | ProBuilder + Synty | Synty Adventure |
| Interiors | No | ProBuilder + Synty | Synty Farm/Town |

---

*Last updated: 2026-04-09*
