# Colors

> The Mereka color system is organized into primitives, foundation colors, and semantic assignments.

**Figma Section**: Styling > Colors

---

## Color Architecture

```
Primitives (raw colors)
    ↓
Foundation (named palette)
    ↓
Semantic (usage-based: primary, success, error, etc.)
```

---

## Primary Brand Colors

Use these for primary actions, brand elements, and key UI.

| Name | Hex | RGB | Usage |
|------|-----|-----|-------|
| **Teal** | `#2d898b` | `rgb(45, 137, 139)` | Primary CTAs, brand accent, active states |
| **Magenta** | `#ab3b78` | `rgb(171, 59, 120)` | Secondary accent, highlights |
| **Blue** | `#295cad` | `rgb(41, 92, 173)` | Links, info states, tertiary actions |

### When to Use What

- **Teal**: Primary buttons, active tabs, selected items, brand moments
- **Magenta**: Special callouts, promotional elements, secondary emphasis
- **Blue**: Text links, informational badges, secondary buttons

---

## Secondary Colors

Supporting colors for variety and categorization.

| Name | Hex | Usage Example |
|------|-----|---------------|
| Burgundy | `#8c002f` | Error states (dark) |
| Pink | `#cd89ae` | Light accents, backgrounds |
| Sky | `#94d1e4` | Info backgrounds, light accents |
| Orange | `#e18437` | Warning states, attention |
| Gold | `#f4be48` | Highlights, ratings, premium |
| Mint | `#8fbec2` | Success backgrounds, calm states |
| Periwinkle | `#7f9dce` | Info accents, links hover |
| Forest | `#2c6e49` | Success states, positive |

---

## Semantic Colors

Use these semantic names in your code, not raw hex values.

### Status Colors

```css
/* Success - confirmations, completed states */
--color-success: #2c6e49;
--color-success-light: #8fbec2;

/* Warning - caution, pending states */
--color-warning: #f4be48;
--color-warning-dark: #e18437;

/* Error - failures, destructive actions */
--color-error: #8c002f;
--color-error-light: #cd89ae;

/* Info - informational, neutral emphasis */
--color-info: #295cad;
--color-info-light: #94d1e4;
```

### UI Colors

```css
/* Backgrounds */
--color-background: #ffffff;
--color-background-subtle: #f8f8f8;
--color-background-muted: #f0f0f0;

/* Text */
--color-text-primary: #1a1a1a;
--color-text-secondary: #666666;
--color-text-muted: #999999;
--color-text-inverse: #ffffff;

/* Borders */
--color-border: #e0e0e0;
--color-border-strong: #cccccc;
```

---

## Tailwind Configuration

```typescript
// tailwind.config.ts
const colors = {
  mereka: {
    teal: '#2d898b',
    magenta: '#ab3b78',
    blue: '#295cad',
    burgundy: '#8c002f',
    pink: '#cd89ae',
    sky: '#94d1e4',
    orange: '#e18437',
    gold: '#f4be48',
    mint: '#8fbec2',
    periwinkle: '#7f9dce',
    forest: '#2c6e49',
  },
  // Semantic aliases
  primary: '#2d898b',
  success: '#2c6e49',
  warning: '#f4be48',
  error: '#8c002f',
  info: '#295cad',
}
```

---

## Usage Examples

### Buttons

```tsx
// Primary action
<button className="bg-mereka-teal text-white hover:bg-mereka-teal/90">
  Book Experience
</button>

// Secondary action
<button className="border border-mereka-teal text-mereka-teal hover:bg-mereka-teal/10">
  Learn More
</button>

// Destructive action
<button className="bg-mereka-burgundy text-white">
  Cancel Booking
</button>
```

### Status Badges

```tsx
// Booking confirmed
<span className="bg-mereka-mint text-mereka-forest">Confirmed</span>

// Payment pending
<span className="bg-mereka-gold/20 text-mereka-orange">Pending</span>

// Error state
<span className="bg-mereka-pink text-mereka-burgundy">Failed</span>
```

### Text

```tsx
// Primary text (never use pure black)
<p className="text-gray-900">Main content</p>

// Secondary text
<p className="text-gray-600">Supporting text</p>

// Muted/caption
<p className="text-gray-400">Timestamp or meta info</p>
```

---

## Don'ts

- **Don't use `#000000`** for text - use `gray-900` or `#1a1a1a`
- **Don't invent colors** - stick to the palette
- **Don't use raw hex in components** - use CSS variables or Tailwind classes
- **Don't mix semantic meanings** - success is green, error is red, always

---

## Color Accessibility

All color combinations should meet WCAG AA contrast ratios:

| Foreground | Background | Ratio | Status |
|------------|------------|-------|--------|
| White | Teal (#2d898b) | 4.6:1 | ✅ AA |
| White | Magenta (#ab3b78) | 4.5:1 | ✅ AA |
| White | Blue (#295cad) | 4.8:1 | ✅ AA |
| White | Burgundy (#8c002f) | 8.2:1 | ✅ AAA |
| Gray-900 | White | 12.6:1 | ✅ AAA |
