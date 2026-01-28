# Typography

> Mereka uses **Lato** for headings and **Poppins** for body text. Both are Google Fonts.

**Figma Section**: Styling > Typography (Desktop)

---

## Font Families

### Lato (Headings)

Used for all headings, navigation items, and UI labels.

```css
font-family: 'Lato', sans-serif;
```

**Weights available**: Regular (400), Bold (700)

### Poppins (Body)

Used for body text, paragraphs, and longer content.

```css
font-family: 'Poppins', sans-serif;
```

**Weights available**: Regular (400), Medium (500), SemiBold (600)

### Open Sans (Video)

Used specifically for video lower thirds (names).

```css
font-family: 'Open Sans', sans-serif;
font-weight: 700; /* Bold */
```

---

## Type Scale

| Name | Size | Line Height | Font | Weight | Usage |
|------|------|-------------|------|--------|-------|
| **Display** | 48px | 1.2 | Lato | Bold | Hero sections, landing pages |
| **H1** | 36px | 1.25 | Lato | Bold | Page titles |
| **H2** | 28px | 1.3 | Lato | Bold | Section headers |
| **H3** | 24px | 1.35 | Lato | Bold | Card titles, subsections |
| **H4** | 20px | 1.4 | Lato | Bold | Small headings |
| **H5** | 18px | 1.4 | Lato | Bold | Labels, list headers |
| **H6** | 16px | 1.4 | Lato | Bold | Small labels |
| **Body Large** | 18px | 1.5 | Poppins | Regular | Lead paragraphs |
| **Body** | 16px | 1.5 | Poppins | Regular | Default body text |
| **Body Small** | 14px | 1.5 | Poppins | Regular | Secondary text |
| **Caption** | 12px | 1.5 | Poppins | Regular | Timestamps, metadata |
| **Overline** | 11px | 1.5 | Poppins | SemiBold | Section labels, uppercase |

---

## CSS Implementation

```css
:root {
  /* Font families */
  --font-heading: 'Lato', sans-serif;
  --font-body: 'Poppins', sans-serif;

  /* Type scale */
  --text-display: 48px;
  --text-h1: 36px;
  --text-h2: 28px;
  --text-h3: 24px;
  --text-h4: 20px;
  --text-h5: 18px;
  --text-h6: 16px;
  --text-body-lg: 18px;
  --text-body: 16px;
  --text-body-sm: 14px;
  --text-caption: 12px;
  --text-overline: 11px;
}
```

---

## Tailwind Configuration

```typescript
// tailwind.config.ts
export default {
  theme: {
    fontFamily: {
      heading: ['Lato', 'sans-serif'],
      body: ['Poppins', 'sans-serif'],
    },
    fontSize: {
      'display': ['48px', { lineHeight: '1.2', fontWeight: '700' }],
      'h1': ['36px', { lineHeight: '1.25', fontWeight: '700' }],
      'h2': ['28px', { lineHeight: '1.3', fontWeight: '700' }],
      'h3': ['24px', { lineHeight: '1.35', fontWeight: '700' }],
      'h4': ['20px', { lineHeight: '1.4', fontWeight: '700' }],
      'h5': ['18px', { lineHeight: '1.4', fontWeight: '700' }],
      'h6': ['16px', { lineHeight: '1.4', fontWeight: '700' }],
      'body-lg': ['18px', { lineHeight: '1.5' }],
      'body': ['16px', { lineHeight: '1.5' }],
      'body-sm': ['14px', { lineHeight: '1.5' }],
      'caption': ['12px', { lineHeight: '1.5' }],
      'overline': ['11px', { lineHeight: '1.5', fontWeight: '600' }],
    },
  },
}
```

---

## Usage Examples

### Page Header

```tsx
<header>
  <h1 className="font-heading text-h1 text-gray-900">
    Discover Experiences
  </h1>
  <p className="font-body text-body-lg text-gray-600 mt-2">
    Find workshops, events, and learning opportunities near you.
  </p>
</header>
```

### Card Title

```tsx
<article className="card">
  <h3 className="font-heading text-h3 text-gray-900">
    Introduction to Pottery
  </h3>
  <p className="font-body text-body-sm text-gray-600 mt-1">
    Learn the basics of wheel throwing in this hands-on workshop.
  </p>
  <span className="font-body text-caption text-gray-400 mt-2">
    2 hours • Beginner friendly
  </span>
</article>
```

### Section with Overline

```tsx
<section>
  <span className="font-body text-overline uppercase tracking-wider text-mereka-teal">
    Featured
  </span>
  <h2 className="font-heading text-h2 text-gray-900 mt-1">
    Popular This Week
  </h2>
</section>
```

---

## Hierarchy Guidelines

### Do

- Use **one H1** per page (the main title)
- Progress headings in order (H1 → H2 → H3, don't skip)
- Use **body text** for readable content
- Use **captions** for metadata, timestamps, counts

### Don't

- Don't use headings for styling (use proper hierarchy)
- Don't mix fonts randomly (Lato = headings, Poppins = body)
- Don't go smaller than 12px (caption size)
- Don't use light font weights (minimum 400)

---

## Responsive Typography

For mobile, scale down the larger sizes:

| Name | Desktop | Mobile |
|------|---------|--------|
| Display | 48px | 36px |
| H1 | 36px | 28px |
| H2 | 28px | 24px |
| H3 | 24px | 20px |
| Body | 16px | 16px (no change) |

```tsx
<h1 className="font-heading text-h2 md:text-h1">
  Responsive Heading
</h1>
```

---

## Loading Fonts

Add to your HTML head or Next.js layout:

```tsx
// app/layout.tsx
import { Lato, Poppins } from 'next/font/google'

const lato = Lato({
  subsets: ['latin'],
  weight: ['400', '700'],
  variable: '--font-heading',
})

const poppins = Poppins({
  subsets: ['latin'],
  weight: ['400', '500', '600'],
  variable: '--font-body',
})

export default function RootLayout({ children }) {
  return (
    <html className={`${lato.variable} ${poppins.variable}`}>
      <body className="font-body">{children}</body>
    </html>
  )
}
```
