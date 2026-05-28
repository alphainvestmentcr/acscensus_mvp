# Ascensus Strategic — Component Specifications

> Tech stack: **Next.js 16** (App Router) · **React 19** · **TypeScript 5** · **Tailwind CSS v4**  
> Design tokens: see [`tokens.css`](./tokens.css)  
> Visual reference: open [`style-guide.html`](./style-guide.html) in a browser

---

## Table of Contents

1. [Design Principles](#design-principles)
2. [Font Setup](#font-setup)
3. [Tailwind v4 Theme Integration](#tailwind-v4-theme-integration)
4. [Navigation](#navigation)
5. [Hero Section](#hero-section)
6. [Metrics Bar](#metrics-bar)
7. [Service Cards](#service-cards)
8. [Quote Block](#quote-block)
9. [Process Steps](#process-steps)
10. [CTA Section](#cta-section)
11. [Footer](#footer)
12. [Buttons](#buttons)
13. [Form Elements](#form-elements)
14. [Typography Components](#typography-components)
15. [Scroll Animations](#scroll-animations)
16. [Accessibility Notes](#accessibility-notes)

---

## Design Principles

| Principle | Application |
|-----------|-------------|
| **Darkness = gravitas** | Near-black canvas (`#0B0C0E`) grounds every page. Never use white or light-grey backgrounds. |
| **Gold = distinction** | `#C6AA4C` is reserved for accent, never used as background fill (except on primary buttons). |
| **Serif = authority** | Cormorant Garamond carries all display headings. Use italics to add warmth and personality. |
| **Sans = clarity** | DM Sans weight 300 handles all body copy. Weight 500 for UI labels and uppercase tracking. |
| **Restraint** | One animation per section. One gold element per component. Negative space is intentional. |
| **Angular by default** | Cards and section containers have zero border radius. Pill radius is reserved for interactive elements. |

---

## Font Setup

Add to `app/layout.tsx`:

```tsx
import { Cormorant_Garamond, DM_Sans } from 'next/font/google';

const cormorant = Cormorant_Garamond({
  subsets: ['latin'],
  weight: ['300', '400', '500', '600'],
  style: ['normal', 'italic'],
  variable: '--font-display',
  display: 'swap',
});

const dmSans = DM_Sans({
  subsets: ['latin'],
  weight: ['300', '400', '500'],
  style: ['normal', 'italic'],
  axes: ['opsz'],
  variable: '--font-sans',
  display: 'swap',
});

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={`${cormorant.variable} ${dmSans.variable}`}>
      <body>{children}</body>
    </html>
  );
}
```

---

## Tailwind v4 Theme Integration

Add to `app/globals.css` after `@import "tailwindcss"`:

```css
@theme inline {
  /* Backgrounds */
  --color-bg-base:    #0B0C0E;
  --color-bg-soft:    #111318;
  --color-bg-elev:    #181C24;
  --color-bg-raised:  #1E2330;

  /* Gold */
  --color-gold:       #C6AA4C;
  --color-gold-soft:  #B09840;
  --color-gold-dim:   rgba(198, 170, 76, 0.55);
  --color-gold-glow:  rgba(198, 170, 76, 0.12);
  --color-gold-line:  rgba(198, 170, 76, 0.18);
  --color-gold-subtle: rgba(198, 170, 76, 0.06);

  /* Text */
  --color-text:       #E8E0CE;
  --color-text-dim:   #A8A090;
  --color-text-mute:  #6E6960;
  --color-text-inverse: #0B0C0E;

  /* Lines */
  --color-line:       rgba(198, 170, 76, 0.18);
  --color-line-soft:  rgba(255, 255, 255, 0.06);
  --color-line-hard:  rgba(255, 255, 255, 0.12);

  /* Fonts */
  --font-display: var(--font-display), "Times New Roman", Georgia, serif;
  --font-sans:    var(--font-sans), system-ui, sans-serif;
  --font-mono:    "JetBrains Mono", "Fira Code", monospace;
}
```

---

## Navigation

### Anatomy

```
┌──────────────────────────────────────────────────────────────┐
│  ● Ascensus Strategic   Services Philosophy Process Contact  │[CTA]│
└──────────────────────────────────────────────────────────────┘
  brand-mark + wordmark      nav-links (desktop)              nav-cta
```

### States

| State | Background | Padding-Y | Border-bottom |
|-------|-----------|-----------|---------------|
| **Default** (at top) | `transparent` | `28px` | `none` |
| **Scrolled** | `rgba(11,12,14,.92)` + `backdrop-filter: blur(20px)` | `16px` | `1px solid --color-line-soft` |
| **Mobile open** | same as scrolled | — | mobile menu expands below |

### Scroll progress bar

- `position: fixed; top: 0; left: 0; right: 0; height: 2px`
- `background: linear-gradient(90deg, --color-gold-soft, --color-gold)`
- `transform-origin: left; transform: scaleX(scrollFraction)`
- `z-index: 9999` (above everything)

### Props (TypeScript interface)

```ts
interface NavProps {
  links: Array<{ href: string; label: string }>;
  ctaLabel: string;
  ctaHref: string;
}
```

### Tailwind class pattern

```tsx
// Nav wrapper
<nav className="fixed top-0 left-0 right-0 z-[1000] transition-[padding,background,border-color,backdrop-filter] duration-400">

// Scrolled modifier (add via JS on scroll)
// bg-[rgba(11,12,14,0.92)] backdrop-blur-xl border-b border-line-soft py-4
// Default: py-7 border-b-transparent

// Nav links
<a className="text-[11px] font-medium tracking-[0.16em] uppercase text-text-mute
              hover:text-gold transition-colors duration-250
              relative after:absolute after:bottom-0 after:left-0 after:right-0
              after:h-px after:bg-gold after:scale-x-0 after:origin-left
              hover:after:scale-x-100 after:transition-transform after:duration-350">
```

### Responsive behaviour

- **≥ 1024px**: Full nav with links + CTA button
- **< 1024px**: Hamburger icon replaces links; drawer slides down
- Mobile menu links have `padding: 16px 40px` and `border-bottom: 1px solid line-soft`

---

## Hero Section

### Anatomy

```
┌──────────────────────────────────────────────────────────────────────┐
│  │                                                                    │
│  │  ◆ EYEBROW LABEL                              [decorative image]  │
│  │                                                                    │
│  │  Large Display Heading with                                        │
│  │  italic gold emphasis word.                                        │
│  │                                                                    │
│  │  Supporting paragraph at --text-lg.                                │
│  │                                                                    │
│  │  [Primary CTA]   [Ghost CTA]                                       │
│  │                                                                    │
│  │ scroll hint                                                        │
│  └─ hero-line (fixed, left edge, gold gradient)                      │
└──────────────────────────────────────────────────────────────────────┘
```

### Visual layers (bottom → top)

1. `background: --color-bg-base`
2. Radial gradient glow behind image: `radial-gradient(ellipse 55% 80% at 88% 55%, rgba(198,162,39,.09), transparent 65%)`
3. Vignette: `radial-gradient(ellipse 80% 80% at 50% 50%, transparent 35%, rgba(0,0,0,.45) 100%)`
4. Gold grid texture (::after)
5. Content grid (z-index 1)
6. Decorative image (right column, mask-image fade to left)

### Layout

```css
/* 2-column on desktop, 1-column on mobile */
display: grid;
grid-template-columns: 55fr 45fr;

/* Mobile */
@media (max-width: 768px) { grid-template-columns: 1fr; }
```

### Hero image treatment

```css
.hero-image {
  position: absolute;
  right: 8%;
  bottom: 0;
  height: 92%;
  object-fit: contain;
  mask-image: linear-gradient(to left, #000 52%, transparent 100%);
  /* Breathing animation */
  animation: hero-breathe 6s ease-in-out infinite;
}

@keyframes hero-breathe {
  0%, 100% { filter: drop-shadow(0 0 60px rgba(198,162,39,.20)); }
  50%       { filter: drop-shadow(0 0 110px rgba(198,162,39,.44)); }
}
```

### Hero line (left edge decoration)

```css
/* Fixed vertical gold line on left side */
position: fixed;
left: 40px;
top: 0;
width: 1px;
height: 100%;
background: linear-gradient(to bottom, transparent 0%, --color-gold-dim 15%, --color-gold-dim 85%, transparent 100%);
opacity: .35;
```

### Scroll dot

Animated dot that travels from `top: 15%` to `top: 85%` via GSAP scroll scrub.

---

## Metrics Bar

### Layout

3-column grid, separated by vertical `--color-line` dividers at 22%–78% height.

```
┌──────────────┬──────────────┬──────────────┐
│  $2.4B+      │  25 yrs      │  18%         │
│  Assets      │  Experience  │  Returns     │
└──────────────┴──────────────┴──────────────┘
```

### Typography

- **Value**: Cormorant Garamond `clamp(44px, 5vw, 64px)` weight 300, `--color-gold`
- **Superscript** (`+`, `yrs`, `%`): 0.42em, `--color-gold-dim`
- **Label**: 10px, tracking `0.16em`, uppercase, `--color-text-mute`

### Counter animation

On scroll entry, values animate from 0 to target using `gsap.to({ val: 0 }, { val: target, duration: 2.2, ease: 'power2.out' })`. Only fires once.

### Background

```css
background: linear-gradient(180deg, --color-bg-base, #0F1014);
border-top: 1px solid --color-line-soft;
border-bottom: 1px solid --color-line-soft;
```

---

## Service Cards

### Grid

```css
/* No gap — cells separated by shared background line */
display: grid;
grid-template-columns: repeat(3, 1fr);
gap: 1px;
background: --color-line-soft;   /* the "gap" colour */
border: 1px solid --color-line-soft;

/* Tablet */
@media (max-width: 1024px) { grid-template-columns: repeat(2, 1fr); }
/* Mobile */
@media (max-width: 768px)  { grid-template-columns: 1fr; }
```

### Individual card anatomy

```
padding: 44px 36px
├── category label   (10px, tracking 0.16em, --color-gold-dim)
├── title            (Cormorant 22px weight 400)
├── description      (14px, --color-text-dim, line-height 1.72)
└── arrow icon       (right-aligned, transitions on hover)
```

### Hover state

```css
.card:hover { background: --color-bg-soft; }

/* Gold underline entrance */
.card::after {
  content: '';
  position: absolute;
  left: 0; bottom: 0; right: 0; height: 2px;
  background: linear-gradient(90deg, --color-gold, --color-gold-soft);
  transform: scaleX(0);
  transform-origin: left;
  transition: transform 400ms --ease-decelerate;
}
.card:hover::after { transform: scaleX(1); }

/* Arrow nudge */
.card:hover .arrow-icon {
  transform: translate(5px, -5px);
  color: --color-gold;
}
```

### Props

```ts
interface ServiceCardProps {
  number:      string;   // e.g. "01"
  category:    string;   // e.g. "Investment"
  title:       string;
  description: string;
  href?:       string;
}
```

---

## Quote Block

### Layout

```
┌──────────────────────────────────────────────────────────────┐
│  Philosophy section                                          │
│                                                              │
│  ┌────────────────────────┐   ┌──────────────────────────┐  │
│  │  " (giant, opacity .07)│   │  i  Independence         │  │
│  │  "Blockquote text in   │   │     Description...       │  │
│  │   Cormorant Garamond   │   │  ii Discretion           │  │
│  │   italic, weight 300." │   │     Description...       │  │
│  │                        │   │  iii Long-term vision    │  │
│  │  — Source · Year       │   │  iv  Analytical rigour   │  │
│  └────────────────────────┘   └──────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
```

### Blockquote typography

- Font: Cormorant Garamond italic, weight 300
- Size: `clamp(19px, 2.2vw, 27px)`
- Line-height: `1.58`
- Colour: `--color-text`

### Cite line

```css
cite {
  font-size: 11px;
  letter-spacing: 0.18em;
  text-transform: uppercase;
  color: --color-text-mute;
  display: flex;
  align-items: center;
  gap: 14px;
}
cite::before {
  content: '';
  width: 24px;
  height: 1px;
  background: --color-gold-dim;
}
```

### Pillars (right column)

Each pillar row:

```
border-bottom: 1px solid --color-line-soft
padding: 28px 0
gap: 24px

pillar-num: Cormorant italic 20px, --color-gold-dim
h4:         Cormorant 18px weight 400
p:          DM Sans 14px weight 300, --color-text-dim
```

---

## Process Steps

### Layout

4-column grid. A horizontal connector line (`z-index: 0`) runs between dot centres.  
A gold progress line (`z-index: 1`) animates in width on scroll entry.

```
    ●─────────────────●- - - - - - - - -○- - - - - - - - -○
   [i]               [ii]              [iii]              [iv]
 Discovery         Strategy          Execution         Stewardship
```

### Dot states

| State | Border | Colour | Background | Shadow |
|-------|--------|--------|------------|--------|
| **done** | `--color-gold-dim` | `--color-gold-dim` | `--color-bg-base` | none |
| **active** | `--color-gold` | `--color-gold` | `--color-bg-elev` | `0 0 22px --color-gold-glow` |
| **idle** | `--color-line-hard` | `--color-text-mute` | `--color-bg-base` | none |

### Progress line animation

```ts
// GSAP — fires once on scroll entry
gsap.to(progressLine, {
  width: 'calc(75% - 28px)',
  duration: 1.6,
  ease: 'power2.inOut',
  delay: 0.3,
});
// Dot illumination — staggered via setTimeout
steps.forEach((step, i) =>
  setTimeout(() => step.classList.add('lit'), 400 + i * 320)
);
```

---

## CTA Section

### Visual structure

```css
/* Centred column, max-width 700px, text-align center */
position: relative;
overflow: hidden;

/* Radial gold glow behind */
background: radial-gradient(ellipse 70% 70% at 50% 50%, rgba(198,170,76,.06), transparent 70%);

/* Gold grid overlay */
background-image:
  linear-gradient(rgba(198,170,76,.04) 1px, transparent 1px),
  linear-gradient(90deg, rgba(198,170,76,.04) 1px, transparent 1px);
background-size: 64px 64px;
```

### Content order (staggered reveal)

1. Section tag (eyebrow)
2. Heading (`clamp(32px, 4vw, 54px)`, Cormorant, italic gold word)
3. Subtext (15px, `--color-text-dim`)
4. Button row (primary + ghost, `justify-content: center`)

---

## Footer

### Grid layout

```css
/* 4-column: brand info, contact, office, navigation */
grid-template-columns: 2fr 1fr 1fr 1fr;
gap: 48px;
margin-bottom: 64px;

/* Tablet */
@media (max-width: 1024px) { grid-template-columns: 1fr 1fr; }
/* Mobile */
@media (max-width: 768px)  { grid-template-columns: 1fr; }
```

### Footer bottom bar

```
┌─────────────────────────────────────────────────────────────────┐
│  © 2025 Ascensus Strategic. All rights reserved.   Legal Privacy│
└─────────────────────────────────────────────────────────────────┘
```

- `border-top: 1px solid --color-line-soft`
- `font-size: 12px; color: --color-text-mute`
- Flex row, space-between

### Column heading

```css
font-size: 10px;
letter-spacing: 0.20em;
text-transform: uppercase;
color: --color-gold;
margin-bottom: 24px;
```

---

## Buttons

### Variants

| Variant | Background | Border | Text | Use case |
|---------|-----------|--------|------|----------|
| `btn-primary` | `--color-gold` | `--color-gold` | `--color-text-inverse` | Primary actions |
| `btn-ghost` | `transparent` | `--color-line-hard` | `--color-text-dim` | Secondary actions |
| `btn-outline` | `transparent` | `--color-gold-line` | `--color-gold` | Tertiary / decorative |
| `btn-icon` | `transparent` | `--color-gold-line` | `--color-gold` | Icon-only, circle |

### Sizes

| Size | Height | Padding-X | Font size |
|------|--------|-----------|-----------|
| `sm` | 36px | 20px | 10px |
| default | 44px | 28px | 11px |
| `lg` | 52px | 36px | 11px |

### Common properties (all variants)

```css
border-radius: 999px;
font-family: --font-sans;
font-weight: 500;
letter-spacing: 0.18em;
text-transform: uppercase;
transition: background 250ms ease, color 250ms ease, box-shadow 250ms ease;
```

### Hover states

- `btn-primary` → transparent background, gold text, gold border, `box-shadow: 0 0 24px --color-gold-glow`
- `btn-ghost` → gold text, gold-dim border
- `btn-icon` → gold background, dark text, gold glow

### Disabled state

```css
opacity: 0.4;
cursor: not-allowed;
pointer-events: none;
```

---

## Form Elements

### Input base

```css
height: 48px;
background: --color-bg-elev;
border: 1px solid --color-line-hard;
border-radius: 4px;           /* --radius-sm */
color: --color-text;
font-family: --font-sans;
font-size: 14px;
font-weight: 300;
padding: 0 16px;
outline: none;
transition: border-color 250ms, box-shadow 250ms;
```

### States

| State | Border | Shadow |
|-------|--------|--------|
| Default | `--color-line-hard` | none |
| Focus | `--color-gold` | `0 0 0 3px --color-gold-glow` |
| Error | `#9C4A4A` | `0 0 0 3px rgba(156,74,74,.12)` |
| Success | `#4A8C6A` | `0 0 0 3px rgba(74,140,106,.12)` |
| Disabled | `--color-line-soft` | none, `opacity: 0.5` |

### Label

```css
font-size: 10px;
letter-spacing: 0.18em;
text-transform: uppercase;
color: --color-text-mute;
margin-bottom: 8px;
```

### Hint / error text

```css
font-size: 11px;
color: --color-text-mute;     /* default hint */
color: #9C4A4A;               /* error */
color: #4A8C6A;               /* success */
margin-top: 6px;
```

---

## Typography Components

### `<Eyebrow>` — section label

```tsx
// Usage: <Eyebrow>Services</Eyebrow>

// Styles:
font-size: 11px (--text-2xs)
letter-spacing: 0.28em (--tracking-caps)
text-transform: uppercase
color: --color-gold
display: flex; align-items: center; gap: 12px

// ::before pseudo-element:
content: '◆'; font-size: 4px; color: --color-gold;
```

### Heading with italic gold emphasis

```tsx
// Cormorant Garamond italic on the accent word
<h2 className="font-display font-light leading-tight tracking-tight">
  A method for a{' '}
  <em className="text-gold italic">lasting relationship</em>.
</h2>
```

### Section head pattern

```
┌─────────────────────────────┬────────────────────────┐
│  ◆ EYEBROW                  │                        │
│  Section Title with         │  Supporting subtext    │
│  italic emphasis.           │  at 15px, text-dim,    │
│                             │  aligned to bottom.    │
└─────────────────────────────┴────────────────────────┘
2-column grid, gap: 48px, align-items: end
```

---

## Scroll Animations

Install GSAP + ScrollTrigger:

```bash
npm install gsap
```

### Reveal pattern (section entry)

```ts
import { gsap } from 'gsap';
import { ScrollTrigger } from 'gsap/ScrollTrigger';

gsap.registerPlugin(ScrollTrigger);

// Standard reveal — apply to any section element
gsap.fromTo(element, 
  { opacity: 0, y: 40 },
  {
    opacity: 1,
    y: 0,
    duration: 1.1,
    ease: 'power3.out',    // --ease-power3
    scrollTrigger: {
      trigger: element,
      start: 'top 80%',
    },
  }
);

// Staggered children
gsap.fromTo(children,
  { opacity: 0, y: 30 },
  {
    opacity: 1,
    y: 0,
    stagger: 0.08,           // --stagger-base × 1
    duration: 0.9,
    ease: 'power3.out',
    scrollTrigger: { trigger: container, start: 'top 75%' },
  }
);
```

### Hero entrance sequence

```ts
const tl = gsap.timeline({ delay: 0.15, defaults: { ease: 'power3.out' } });

tl.to(heroLine,    { scaleY: 1,                duration: 1.8, ease: 'power2.inOut' }, 0)
  .to(heroImage,   { opacity: 0.96, x: 0,      duration: 1.0, ease: 'power2.out' }, 0.1)
  .to(eyebrow,     { opacity: 1, y: 0,         duration: 0.9 }, 0.4)
  .to(heading,     { opacity: 1, y: 0,         duration: 1.1 }, 0.65)
  .to(paragraph,   { opacity: 1, y: 0,         duration: 0.9 }, 0.9)
  .to(ctaRow,      { opacity: 1, y: 0,         duration: 0.8 }, 1.1)
  .to(scrollHint,  { opacity: 1, y: 0,         duration: 0.7 }, 1.3);
```

### Reduced motion

Always wrap animations:

```ts
const prefersReduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
if (!prefersReduced) {
  // run GSAP animations
}
```

And in CSS:

```css
@media (prefers-reduced-motion: reduce) {
  * { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
}
```

---

## Accessibility Notes

| Component | Requirement |
|-----------|-------------|
| **Navigation** | `aria-label="Main navigation"` on `<nav>`; hamburger button has `aria-expanded` and `aria-controls` |
| **Hero image** | Decorative images use `aria-hidden="true"` and empty `alt=""` |
| **Metrics counter** | Add `aria-live="polite"` to the counter element so screen readers announce the final value |
| **Service cards** | Use `<article>` element; ensure arrow icon has `aria-hidden="true"` |
| **Process steps** | Use `<ol>` for the step list; each step's dot number is decorative — use `aria-hidden` |
| **Buttons** | Icon-only buttons require `aria-label` |
| **Form inputs** | All inputs must have an associated `<label>` (not just placeholder text) |
| **Focus rings** | The gold focus ring (`box-shadow: 0 0 0 3px --color-gold-glow`) must have 3:1 contrast ratio minimum |
| **Colour contrast** | `--color-text` (#E8E0CE) on `--color-bg-base` (#0B0C0E) → **12.5:1** ✅ |
| | `--color-gold` (#C6AA4C) on `--color-bg-base` (#0B0C0E) → **6.2:1** ✅ |
| | `--color-text-dim` (#A8A090) on `--color-bg-base` → **5.8:1** ✅ |
| | `--color-text-mute` (#6E6960) on `--color-bg-base` → **3.1:1** ✅ (large text only) |

---

*Ascensus Strategic Design System · v1.0 · 2025*
