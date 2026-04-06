# MONOLITH — Where Creation Dissolves

> **"We don't push boundaries. We erase them."**

A single-file, dark cinematic landing page that opens with a terminal boot sequence and closes with a CSS glitch. Between those two moments: 30+ interactions, zero dependencies, and one very opinionated design philosophy.

The page doesn't load. It **boots**.

![MONOLITH — dark cinematic landing page with WebGL sphere](https://picsum.photos/seed/monolith-readme/1400/800)

**[▶ Live Demo](https://smart-unknown.github.io/monolith-landing/)**

---

## ✦ First 3 Seconds

Most landing pages fade in. MONOLITH doesn't trust you with content until it's ready:

```
> Initializing spatial matrix...
> Loading neural pathways        [OK]
> Calibrating perception engine [OK]
> Compiling reality shader       [OK]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
         MONOLITH
```

Four terminal lines appear sequentially. A progress bar fills. The title flickers 4 times — a deliberate CRT artifact — then locks on. The boot screen dissolves. Only then does the page reveal itself.

This isn't decoration. It's **tone-setting before first content**.

---

## ✦ The 30 Interactions

### Boot Sequence (0s–2.8s)
| # | Effect | Implementation |
|---|--------|---------------|
| 1 | Terminal line stagger | `setTimeout` chain, 350ms intervals |
| 2 | Progress bar fill | CSS `width` transition, 1.5s |
| 3 | Title flicker | `@keyframes flick` — opacity 1→0.15, 4 iterations |
| 4 | Boot dissolve | `opacity:0` + `pointer-events:none` animation |

### Hero (2.8s+)
| # | Effect | Implementation |
|---|--------|---------------|
| 5 | Typewriter subtitle | JS `insertBefore` loop, 42ms/char, auto-removing cursor |
| 6 | Staggered button reveal | Inline `opacity`/`transform` transitions |
| 7 | WebGL morphing sphere | Raw vertex + fragment shaders, 64×40 mesh |
| 8 | 4-octave noise displacement | GLSL `for` loop, amplitude halving per octave |
| 9 | Mouse-reactive bulge | `dot(normal, mouseDir)` vertex displacement |
| 10 | Dual-axis rotation | Y-axis 0.1 rad/s + oscillating X-axis |
| 11 | Fresnel rim lighting | `pow(1 - abs(dot(N, V)), 3.0)` |
| 12 | Perspective grid floor | CSS `rotateX(65deg)` + animated `background-position` |
| 13 | Floating geometric shapes | 8 shapes, randomized duration/delay via CSS variables |
| 14 | Motion-blur cursor trail | Canvas 2D polyline with `taper` (lineWidth scales with age) |
| 15 | Cursor dot + ring | DOM elements, 0.14 lerp damping on ring |
| 16 | Ambient mouse glow | 500px radial gradient following cursor |

### Page-Wide
| # | Effect | Implementation |
|---|--------|---------------|
| 17 | Triple click ripple | 3 staggered `scale(0→55)` rings at 90ms intervals |
| 18 | Gradient scroll progress | 3-color `linearGradient` bar, `scaleX` driven by scroll |
| 19 | Header backdrop blur | `backdrop-filter: blur(20px)` on scroll > 50px |
| 20 | Dual marquee | Two rows, opposite directions, different speeds |
| 21 | Text scramble (nav) | 18 random-char frames on hover, 350ms total |

### Manifesto
| # | Effect | Implementation |
|---|--------|---------------|
| 22 | Clip-path reveal | `inset(0 100% 0 0)` → `inset(0 0% 0 0)` per line |
| 23 | Scroll-driven word morph | 4 word pairs cycling via `scrollY` progress + scramble |

### Showcase
| # | Effect | Implementation |
|---|--------|---------------|
| 24 | 3D perspective tilt | `perspective(800px) rotateY(±12°) rotateX(±12°)` |
| 25 | Radial shine follow | CSS custom property `--sx`/`--sy` driven by mouse |
| 26 | Holographic shimmer | `linear-gradient` at `atan2` angle + `mix-blend-mode: overlay` |
| 27 | Image parallax | 8px inverse translation inside tilted card |

### Features
| # | Effect | Implementation |
|---|--------|---------------|
| 28 | Rotating conic-gradient border | `@property --ba` Houdini API + `conic-gradient` animation |

### Stats
| # | Effect | Implementation |
|---|--------|---------------|
| 29 | SVG ring progress | `stroke-dashoffset` transition on scroll-into-view |
| 30 | Number counter | `requestAnimationFrame` + easeOut quadratic |

### CTA
| # | Effect | Implementation |
|---|--------|---------------|
| 31 | CSS glitch text | `::before` coral + `::after` cyan with `clip-path` jitter |
| 32 | Morphing blobs | Animated `border-radius` + `transform` + 90px blur |

---

## ✦ WebGL Shader Breakdown

The sphere isn't using Three.js or any library. Raw WebGL from scratch.

### Vertex Shader — Noise Displacement

```glsl
// 4 octaves of value noise for organic displacement
float d = 0.0, amp = 1.0, fr = 3.0;
for (int i = 0; i < 4; i++) {
    d += ns(normal.xy * fr + uTime * (0.3 - float(i) * 0.06)) * amp;
    amp *= 0.5;
    fr *= 2.0;
}
d *= 0.1;

// Mouse-reactive directional bulge
vec3 mouseDir = normalize(vec3(uMouse * 1.5, 0.8));
d += max(0.0, dot(normal, mouseDir)) * 0.15 * sin(uTime * 2.0) * uHover;

// Global breathing
d += sin(uTime * 0.8) * 0.02;
```

### Fragment Shader — Fresnel + Gradient

```glsl
// Fresnel rim — edges glow brighter
float fresnel = pow(1.0 - abs(dot(normal, viewDir)), 3.0);

// Color mixing — lime-to-coral based on vertical position + displacement
vec3 color = mix(lime * 0.4, coral * 0.3, clamp(yMix, 0.0, 1.0));
color += abs(displacement) * vec3(0.2, 0.15, 0.05);
color += fresnel * mix(lime, coral, fresnel) * 0.5;

// Final composite — dark core, bright edges
color = mix(dark, color, 0.6 + fresnel * 0.4);
```

**Geometry:** 64 segments × 40 rings = 2,640 vertices, ~7,680 triangles. Single `drawElements` call. Zero post-processing.

---

## ✦ Scroll-Driven Word Morph

The manifesto contains two highlighted words that change as you scroll through the section:

```
Scroll 0%:   We don't push boundaries. We erase them.
Scroll 25%:  We don't push limits. We shatter them.
Scroll 50%:  We don't push rules. We dissolve them.
Scroll 75%:  We don't push walls. We vaporize them.
```

Each transition uses the text scramble effect (18 frames of random characters) so the swap feels computational, not decorative. The scroll position is calculated as a 0–1 ratio of the section's visibility, then mapped to word-pair indices.

---

## ✦ Holographic Shimmer

Each showcase card has a hidden overlay that activates on hover:

```css
.holo {
  background: linear-gradient(
    var(--ha, 135deg),
    transparent 30%,
    rgba(205,255,0,0.07) 42%,
    rgba(0,212,255,0.05) 50%,
    rgba(255,51,85,0.07) 58%,
    transparent 70%
  );
  mix-blend-mode: overlay;
}
```

The angle `--ha` is calculated from cursor position using `Math.atan2`, so the rainbow band physically follows your mouse — just like real holographic foil. Combined with the 3D tilt, it creates a convincing physicality that flat CSS can't achieve.

---

## ✦ Motion-Blur Cursor Trail

Not dots. Not a fading circle. A **tapered polyline**:

```js
for (let i = 1; i < points.length; i++) {
  const t = i / points.length;  // 0 = oldest, 1 = newest
  ctx.lineWidth = t * 3.5;     // Thin at tail, thick at head
  ctx.strokeStyle = `rgba(205,255,0,${t * 0.2})`;  // Faint at tail
  ctx.beginPath();
  ctx.moveTo(points[i-1].x, points[i-1].y);
  ctx.lineTo(points[i].x, points[i].y);
  ctx.stroke();
}
```

28 points stored, oldest shifted out. The result looks like a comet tail — smooth, directional, and fast. Combined with the ambient glow canvas, it creates a cursor system with three distinct layers of feedback.

---

## ✦ Tech Stack

```
HTML5           → Semantic structure
CSS3            → @property, clip-path, backdrop-filter, conic-gradient
WebGL           → Raw vertex/fragment shaders, no libraries
Canvas 2D       → Cursor trail
Vanilla JS      → Zero frameworks, zero build tools
Tailwind CSS    → CDN utility classes
Iconify         → Phosphor Icons via CDN
Google Fonts    → Syne · DM Sans · JetBrains Mono
```

**npm packages: 0. Build steps: 0. Config files: 0. Total files: 1.**

---

## ✦ Color System

| Token | Hex | Role |
|-------|-----|------|
| `ml-bg` | `#050507` | Void — darker than standard `#000` |
| `ml-el` | `#0C0C14` | Elevated surfaces |
| `ml-border` | `#1A1A26` | Invisible-when-not-looking borders |
| `ml-text` | `#F0F0F5` | Near-white with cool undertone |
| `ml-muted` | `#6B6B80` | Secondary text |
| `ml-lime` | `#CDFF00` | **The signature.** Acid green. Aggressive. |
| `ml-coral` | `#FF3355` | Heat, urgency, danger |
| `ml-cyan` | `#00D4FF` | Cold, depth, technology |
| `ml-dim` | `#2A2A3A` | Muted labels |

The lime (`#CDFF00`) is the anchor. It appears in the boot screen, the cursor, the trail, the ripples, the progress bar, the grid, the manifesto underline, the card accents, the stat rings, the CTA button, and the footer logo. **Every glow in this page is lime.** That consistency is what makes it feel like a designed system, not a collection of effects.

---

## ✦ Font Choice

**Syne** — geometric, slightly alien, aggressively modern. The `800` weight at `5.5rem` creates a brutalist editorial feel that Inter or Space Grotesk physically cannot produce. It's the typography equivalent of the lime green: deliberately uncomfortable in a way that demands attention.

---

## ✦ Performance

| Concern | Solution |
|---------|----------|
| WebGL render | Single draw call, no post-processing, no framebuffers |
| Canvas trail | 28 points max, oldest spliced, single `clearRect` per frame |
| Cursor | `will-change: transform` on all 3 cursor layers |
| Resize | Debounced 250ms for canvases, instant for DOM |
| Observers | All `unobserve` after first trigger — zero redundant callbacks |
| Touch | `pointer: coarse` media query hides cursor, trail, ripples, mouse parallax |
| Boot | Entire boot sequence is CSS transitions — no layout thrash |
| Reduced motion | Single `prefers-reduced-motion` query disables every animation |

---

## ✦ File Structure

```
monolith-landing/
└── index.html    ← 32 interactions. One file. No mercy.
```

---

## ✦ Quick Start

```bash
git clone https://github.com/smart-unknown/monolith-landing.git
cd monolith-landing
open index.html
```

No `npm install`. No `npm run dev`. No `node_modules`. No `vite.config.ts`. No `tailwind.config.js`.

Open the file. Watch it boot.

---

## ✦ Browser Support

| Browser | Version | Notes |
|---------|---------|-------|
| Chrome | 90+ | Full support including `@property` |
| Firefox | 128+ | `@property` supported from 128 — borders degrade to static gradient |
| Safari | 15.4+ | Full support |
| Edge | 90+ | Full support |

**Graceful degradation:** If `@property` isn't available, rotating borders show as static conic gradients. If WebGL fails, the sphere canvas hides and the hero falls back to grid + shapes. Everything else is pure CSS or ES5-compatible JS.

---

## ✦ Credits

- **Fonts** — [Syne](https://fonts.google.com/specimen/Syne) · [DM Sans](https://fonts.google.com/specimen/DM+Sans) · [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono)
- **Icons** — [Phosphor Icons](https://phosphoricons.com/) via Iconify
- **Images** — [Picsum Photos](https://picsum.photos)
- **Attitude** — Every terminal boot screen that made you feel like you were about to do something dangerous.
