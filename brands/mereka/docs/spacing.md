# Spacing & Elevation

> Consistent spacing and elevation create visual hierarchy and rhythm.

**Figma Section**: Styling > Spacing, Elevation

---

## Spacing Scale

Mereka uses a **4px base unit**. All spacing should be multiples of 4.

| Token | Value | Usage |
|-------|-------|-------|
| `space-0` | 0px | No spacing |
| `space-1` | 4px | Tight spacing, icon gaps |
| `space-2` | 8px | Inline elements, small gaps |
| `space-3` | 12px | Related items |
| `space-4` | 16px | Standard gap, paragraph spacing |
| `space-5` | 20px | Section padding (small) |
| `space-6` | 24px | Card padding, medium gaps |
| `space-8` | 32px | Section gaps |
| `space-10` | 40px | Large gaps |
| `space-12` | 48px | Section padding (large) |
| `space-16` | 64px | Major section breaks |
| `space-20` | 80px | Page sections |
| `space-24` | 96px | Hero sections |

---

## CSS Variables

```css
:root {
  --space-0: 0px;
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 20px;
  --space-6: 24px;
  --space-8: 32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;
  --space-20: 80px;
  --space-24: 96px;
}
```

---

## Common Spacing Patterns

### Card Padding

```tsx
// Standard card
<div className="p-6">  {/* 24px */}
  <h3>Card Title</h3>
  <p className="mt-2">Description</p>  {/* 8px gap */}
</div>

// Compact card
<div className="p-4">  {/* 16px */}
  <h4>Compact Card</h4>
</div>
```

### Button Groups

```tsx
// Horizontal buttons
<div className="flex gap-3">  {/* 12px gap */}
  <Button>Cancel</Button>
  <Button>Save</Button>
</div>
```

### Form Fields

```tsx
// Stacked form fields
<div className="space-y-4">  {/* 16px between fields */}
  <Input label="Name" />
  <Input label="Email" />
  <Input label="Message" />
</div>
```

### Section Spacing

```tsx
// Page sections
<section className="py-16">  {/* 64px vertical padding */}
  <div className="container">
    <h2 className="mb-8">Section Title</h2>  {/* 32px below */}
    <div className="grid gap-6">  {/* 24px grid gap */}
      {/* Cards */}
    </div>
  </div>
</section>
```

---

## Elevation (Shadows)

Elevation creates depth and indicates interactive elements.

| Level | Shadow | Usage |
|-------|--------|-------|
| **Elevation 0** | None | Flat elements, backgrounds |
| **Elevation 1** | `0 1px 2px rgba(0,0,0,0.05)` | Subtle lift, hover states |
| **Elevation 2** | `0 4px 6px rgba(0,0,0,0.1)` | Cards, raised elements |
| **Elevation 3** | `0 10px 15px rgba(0,0,0,0.1)` | Modals, overlays |
| **Elevation 4** | `0 20px 25px rgba(0,0,0,0.15)` | Floating elements |
| **Elevation 5** | `0 25px 50px rgba(0,0,0,0.25)` | Dialogs, top-level |

### CSS Variables

```css
:root {
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
  --shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.15);
  --shadow-2xl: 0 25px 50px rgba(0, 0, 0, 0.25);
}
```

### Tailwind Usage

```tsx
// Card
<div className="shadow-md hover:shadow-lg transition-shadow">
  Card content
</div>

// Dropdown
<div className="shadow-lg">
  Dropdown menu
</div>

// Modal
<div className="shadow-2xl">
  Modal content
</div>
```

---

## Border Radius

| Token | Value | Usage |
|-------|-------|-------|
| `rounded-none` | 0px | Sharp corners (rare) |
| `rounded-sm` | 4px | Subtle rounding |
| `rounded-md` | 8px | Inputs, small cards |
| `rounded-lg` | 12px | Cards, containers |
| `rounded-xl` | 16px | Large cards, modals |
| `rounded-full` | 9999px | Buttons, pills, avatars |

### Common Patterns

```tsx
// Buttons - always full rounded
<button className="rounded-full">Click me</button>

// Cards - medium to large rounded
<div className="rounded-lg">Card</div>
<div className="rounded-xl">Large card</div>

// Inputs - medium rounded
<input className="rounded-md" />

// Avatars - fully rounded
<img className="rounded-full" />

// Tags/badges - full rounded
<span className="rounded-full">Tag</span>
```

---

## Z-Index Hierarchy

| Token | Value | Usage |
|-------|-------|-------|
| `z-0` | 0 | Default layer |
| `z-10` | 10 | Raised elements |
| `z-20` | 20 | Dropdowns |
| `z-30` | 30 | Sticky headers |
| `z-40` | 40 | Modal backdrop |
| `z-50` | 50 | Modals, dialogs |
| `z-60` | 60 | Popovers |
| `z-70` | 70 | Tooltips |
| `z-max` | 9999 | Critical overlays |

```tsx
// Sticky header
<header className="sticky top-0 z-30">

// Modal backdrop
<div className="fixed inset-0 bg-black/50 z-40">

// Modal content
<div className="fixed z-50">

// Tooltip (always on top)
<div className="z-70">
```

---

## Responsive Spacing

Scale down spacing on mobile:

| Desktop | Mobile |
|---------|--------|
| `py-16` (64px) | `py-12` (48px) |
| `gap-6` (24px) | `gap-4` (16px) |
| `p-6` (24px) | `p-4` (16px) |

```tsx
<section className="py-12 md:py-16">
  <div className="grid gap-4 md:gap-6">
    {/* Content */}
  </div>
</section>
```

---

## Don'ts

- **Don't use arbitrary spacing** - Stick to the scale (no `mt-[13px]`)
- **Don't mix spacing systems** - Use Tailwind classes consistently
- **Don't forget mobile** - Reduce spacing on smaller screens
- **Don't over-elevate** - Most elements don't need shadows
