# Buttons

> Buttons are the primary interactive elements. Mereka has **112 button variants** covering all combinations of style, size, state, and icon options.

**Figma Section**: Buttons

---

## Button Matrix

| Property | Options |
|----------|---------|
| **Style** | Solid, Outline, Ghost, Ghost-inverse, Text |
| **Size** | Default (36px), Large (48px) |
| **State** | Enabled, Hovered, Active, Disabled |
| **Icon** | With icon, Without icon |
| **Label** | With label, Icon-only |

---

## Button Styles

### 1. Solid (Primary)

The default, high-emphasis button for primary actions.

```tsx
<button className="
  bg-gray-900 text-white
  px-4 py-2 rounded-full
  hover:bg-gray-800
  disabled:bg-gray-300 disabled:cursor-not-allowed
">
  Book Now
</button>
```

**Use for**: Primary CTAs, form submissions, critical actions

### 2. Outline (Secondary)

Medium emphasis for secondary actions.

```tsx
<button className="
  border border-gray-900 text-gray-900 bg-transparent
  px-4 py-2 rounded-full
  hover:bg-gray-100
  disabled:border-gray-300 disabled:text-gray-300
">
  Learn More
</button>
```

**Use for**: Secondary actions, cancel buttons, alternatives

### 3. Ghost

Low emphasis, blends into the background.

```tsx
<button className="
  text-gray-900 bg-transparent
  px-4 py-2 rounded-full
  hover:bg-gray-100
  disabled:text-gray-300
">
  View Details
</button>
```

**Use for**: Tertiary actions, toolbar buttons, navigation items

### 4. Ghost-inverse

For use on dark backgrounds.

```tsx
<button className="
  text-white bg-transparent
  px-4 py-2 rounded-full
  hover:bg-white/10
  disabled:text-white/50
">
  Explore
</button>
```

**Use for**: Buttons on dark hero sections, overlays

### 5. Text

Minimal button that looks like a link.

```tsx
<button className="
  text-mereka-teal bg-transparent
  px-2 py-1
  hover:underline
  disabled:text-gray-300
">
  See all
</button>
```

**Use for**: Inline actions, "see more" links, minor actions

---

## Button Sizes

### Default (36px height)

```tsx
<button className="h-9 px-4 text-sm">
  Default Button
</button>
```

### Large (48px height)

```tsx
<button className="h-12 px-6 text-base">
  Large Button
</button>
```

---

## Button with Icon

### Icon + Label

```tsx
<button className="inline-flex items-center gap-2 px-4 py-2">
  <SearchIcon className="w-4 h-4" />
  Search
</button>
```

### Icon Only

```tsx
<button className="p-2 rounded-full" aria-label="Search">
  <SearchIcon className="w-5 h-5" />
</button>
```

---

## Button States

### Enabled (Default)

```tsx
className="bg-gray-900 text-white cursor-pointer"
```

### Hovered

```tsx
className="bg-gray-800 text-white" // Slightly lighter
```

### Active (Pressed)

```tsx
className="bg-gray-950 text-white" // Slightly darker
```

### Disabled

```tsx
className="bg-gray-300 text-gray-500 cursor-not-allowed"
```

### Focused

```tsx
className="ring-2 ring-mereka-teal ring-offset-2"
```

---

## Full Component (CVA)

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const buttonVariants = cva(
  // Base styles
  'inline-flex items-center justify-center rounded-full font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 disabled:pointer-events-none',
  {
    variants: {
      variant: {
        solid: 'bg-gray-900 text-white hover:bg-gray-800 disabled:bg-gray-300',
        outline: 'border border-gray-900 text-gray-900 hover:bg-gray-100 disabled:border-gray-300 disabled:text-gray-300',
        ghost: 'text-gray-900 hover:bg-gray-100 disabled:text-gray-300',
        'ghost-inverse': 'text-white hover:bg-white/10 disabled:text-white/50',
        text: 'text-mereka-teal hover:underline disabled:text-gray-300',
      },
      size: {
        default: 'h-9 px-4 text-sm',
        large: 'h-12 px-6 text-base',
        icon: 'h-9 w-9',
        'icon-large': 'h-12 w-12',
      },
    },
    defaultVariants: {
      variant: 'solid',
      size: 'default',
    },
  }
)

interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export function Button({ className, variant, size, ...props }: ButtonProps) {
  return (
    <button
      className={buttonVariants({ variant, size, className })}
      {...props}
    />
  )
}
```

---

## Usage Examples

### Primary Action

```tsx
<Button variant="solid" size="large">
  Book Experience
</Button>
```

### Form Actions

```tsx
<div className="flex gap-3">
  <Button variant="outline">Cancel</Button>
  <Button variant="solid">Save Changes</Button>
</div>
```

### Card Actions

```tsx
<Button variant="ghost" size="default">
  <HeartIcon className="w-4 h-4 mr-2" />
  Save
</Button>
```

### Hero Section (Dark Background)

```tsx
<div className="bg-gray-900 p-8">
  <Button variant="ghost-inverse" size="large">
    Explore Experiences
  </Button>
</div>
```

---

## Special Buttons

### Social Login Buttons

```tsx
// Google
<button className="flex items-center gap-3 w-full border border-gray-300 rounded-full px-4 py-3 hover:bg-gray-50">
  <GoogleIcon className="w-5 h-5" />
  Continue with Google
</button>

// Facebook
<button className="flex items-center gap-3 w-full bg-[#1877F2] text-white rounded-full px-4 py-3 hover:bg-[#166FE5]">
  <FacebookIcon className="w-5 h-5" />
  Continue with Facebook
</button>
```

### Pagination Buttons

```tsx
<nav className="flex items-center gap-1">
  <button className="w-9 h-9 rounded-full hover:bg-gray-100">
    <ChevronLeftIcon />
  </button>
  <button className="w-9 h-9 rounded-full bg-gray-900 text-white">1</button>
  <button className="w-9 h-9 rounded-full hover:bg-gray-100">2</button>
  <button className="w-9 h-9 rounded-full hover:bg-gray-100">3</button>
  <button className="w-9 h-9 rounded-full hover:bg-gray-100">
    <ChevronRightIcon />
  </button>
</nav>
```

---

## Don'ts

- **Don't use custom colors** - Stick to solid/outline/ghost variants
- **Don't skip hover states** - All buttons need hover feedback
- **Don't use square buttons** - Always `rounded-full`
- **Don't mix sizes** - Keep buttons consistent within a section
- **Don't forget focus states** - Essential for accessibility
