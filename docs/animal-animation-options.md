# Animal & Quadruped Animation Options

Per-asset decisions — review this when adding each animal to the game. The right tool depends on the animal, its role, and how much personality it needs.

---

## Option A: Unity Asset Store Packs
**Best for:** Locomotion baselines (walk, idle, eat, sleep) for common farm animals  
**Strengths:**
- Fastest to production — working rig + animations on day one
- Many low-poly farm animal packs exist (search: "low poly farm animals")
- Already Unity-compatible, often URP-ready
- Saves rigging time entirely
- Can mix with custom animations on top

**Weaknesses:**
- Generic — every game using the same pack looks the same
- Style may not match your exact aesthetic without shader adjustments
- Rig structure may resist customization
- One-time cost per pack, quality varies wildly

**Pricing:** $10–$80 per pack typically  
**Where to look:** Unity Asset Store → search "low poly farm animals"  
**Decision trigger:** Use this when the animal is background/ambient and doesn't need unique personality

---

## Option B: Cascadeur
**Best for:** Signature personality animations — the moments that define each animal  
**Strengths:**
- AI-assisted physics (auto-poses secondary motion like belly jiggle, ear flop)
- Designed for expressive, snappy keyframe animation
- Physics follow-through built in — great for "pig flops onto belly" style moments
- FBX export → Unity compatible
- Works on any rig (humanoid or quadruped)

**Weaknesses:**
- Learning curve — not instant like Mixamo
- You still need a rig before animating (doesn't generate rigs)
- Free tier limits export and features
- Slower per-animation than library-based approaches

**Pricing:** Free (limited export), Indie ~$12/mo, Pro ~$60/mo  
**URL:** https://cascadeur.com  
**Decision trigger:** Use this for any animation that is a character moment — reactions, personality idles, event animations (feeding, petting, happy/sad states)

---

## Option C: ActorCore / AccuRIG (Reallusion)
**Best for:** Auto-rigging non-humanoid characters + motion library  
**Strengths:**
- Supports quadruped auto-rigging
- Has an animal motion library (walk, trot, run cycles)
- FBX export
- More animal coverage than Mixamo

**Weaknesses:**
- Smaller community than Mixamo
- Animal library is thinner than humanoid library
- Credit-based pricing can add up
- Quality of auto-rig varies by animal silhouette

**Pricing:** Credit-based, some free assets, subscriptions available  
**URL:** https://actorcore.reallusion.com  
**Decision trigger:** Use this when you need a clean locomotion rig for an animal Mixamo won't touch and you don't want to hand-rig in Blender

---

## Option D: Procedural Animation (Unity Animation Rigging)
**Best for:** Animals that interact with terrain — legs that adapt to ground slope  
**Strengths:**
- Legs/feet automatically plant correctly on uneven terrain
- No animation frames to author for locomotion
- Scales to any terrain without rework
- Very "alive" feeling movement

**Weaknesses:**
- Code-driven — requires Unity Animation Rigging package knowledge
- Doesn't replace expressive/personality animations
- More complex to set up initially
- Doesn't help with upper-body or reaction animations

**Pricing:** Free (Unity package)  
**Decision trigger:** Use this as a layer on top of other animations for animals that roam your farm terrain, to avoid foot-sliding artifacts

---

## Option E: Blender Manual Rigging + Animation
**Best for:** Unique creatures that don't fit any auto-rigger  
**Strengths:**
- Full control over rig and animation
- Can achieve any result given enough time

**Weaknesses:**
- Steep learning curve
- Very time-consuming
- Against our minimal-Blender goal
- Only use as last resort

**Pricing:** Free  
**Decision trigger:** Only if the animal is so unusual that no other tool produces an acceptable rig

---

## Recommended Approach Per Animal Type

| Animal | Rig Source | Locomotion | Personality Animations |
|--------|-----------|------------|----------------------|
| Pig | Asset Store pack or AccuRIG | Asset Store / AccuRIG library | Cascadeur |
| Cow | Asset Store pack or AccuRIG | Asset Store / AccuRIG library | Cascadeur |
| Chicken | Asset Store pack | Asset Store | Cascadeur |
| Sheep | Asset Store pack or AccuRIG | Asset Store / AccuRIG library | Cascadeur |
| Unique/custom creatures | AccuRIG or Blender | AccuRIG + procedural | Cascadeur |

**Layering strategy:** Asset Store rig → AccuRIG locomotion (if needed) → Cascadeur personality → Unity Animation Rigging for terrain adaptation

---

*Last updated: 2026-04-09 — review per animal as they enter production*
