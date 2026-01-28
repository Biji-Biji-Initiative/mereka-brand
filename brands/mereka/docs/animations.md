# Animations

> Animations bring the Mereka interface to life with purposeful motion. This guide covers **transition standards**, **component animations**, **loading states**, **micro-interactions**, and **accessibility considerations** for reduced motion preferences.

**Figma Section**: Motion, Interactions, Transitions

---

## Animation Principles

### Purpose-Driven Motion

| Principle | Description | Example |
|-----------|-------------|---------|
| **Feedback** | Confirm user actions | Button press, form submission |
| **Orientation** | Show spatial relationships | Page transitions, modal open/close |
| **Focus** | Direct attention | Toast enter, highlight pulse |
| **Delight** | Add personality (sparingly) | Success celebration, onboarding |

### Motion Hierarchy

```
Animation Priority
├── Functional (required)
│   ├── Loading indicators
│   ├── State transitions
│   └── Feedback animations
├── Informational (recommended)
│   ├── Component enter/exit
│   ├── Page transitions
│   └── Scroll reveals
└── Decorative (optional)
    ├── Hover effects
    ├── Micro-interactions
    └── Celebratory moments
```

---

## 1. Transition Standards

### Duration Scale

| Token | Duration | Use Case |
|-------|----------|----------|
| `duration-75` | 75ms | Instant feedback (active states) |
| `duration-100` | 100ms | Quick transitions (focus rings) |
| `duration-150` | 150ms | Standard transitions (hover states) |
| `duration-200` | 200ms | Component transitions (dropdowns, tooltips) |
| `duration-300` | 300ms | Larger transitions (modals, page elements) |
| `duration-500` | 500ms | Complex animations (page transitions) |

### When to Use Each Duration

```tsx
// 75ms - Instant feedback
<button className="active:scale-95 transition-transform duration-75">
  Click me
</button>

// 150ms - Standard hover transitions
<button className="hover:bg-gray-100 transition-colors duration-150">
  Hover me
</button>

// 200ms - Component state changes
<div className="opacity-0 data-[state=open]:opacity-100 transition-opacity duration-200">
  Dropdown content
</div>

// 300ms - Larger component animations
<dialog className="scale-95 data-[state=open]:scale-100 transition-transform duration-300">
  Modal content
</dialog>

// 500ms - Page transitions
<main className="opacity-0 animate-in fade-in duration-500">
  Page content
</main>
```

---

### Easing Curves

| Easing | CSS Value | Token | Use Case |
|--------|-----------|-------|----------|
| **ease-out** | `cubic-bezier(0, 0, 0.2, 1)` | `ease-out` | Enter animations (elements appearing) |
| **ease-in** | `cubic-bezier(0.4, 0, 1, 1)` | `ease-in` | Exit animations (elements leaving) |
| **ease-in-out** | `cubic-bezier(0.4, 0, 0.2, 1)` | `ease-in-out` | Continuous animations (looping) |
| **linear** | `linear` | `linear` | Progress bars, loading spinners |

### Easing Guidelines

```tsx
// ease-out: Elements entering the view
// Starts fast, ends slow - feels responsive
<div className="translate-y-4 data-[visible=true]:translate-y-0 transition-transform duration-300 ease-out">
  Content appearing
</div>

// ease-in: Elements leaving the view
// Starts slow, ends fast - natural exit
<div className="data-[leaving=true]:opacity-0 transition-opacity duration-200 ease-in">
  Content disappearing
</div>

// ease-in-out: Continuous/looping animations
// Smooth throughout - comfortable for repeated motion
<div className="animate-pulse ease-in-out">
  Pulsing element
</div>

// linear: Progress indicators
// Constant speed - accurate representation of progress
<div className="transition-[width] duration-300 linear" style={{ width: `${progress}%` }}>
  Progress bar
</div>
```

---

### Tailwind Configuration

Add these animation utilities to your `tailwind.config.ts`:

```typescript
// tailwind.config.ts
import type { Config } from 'tailwindcss'

const config: Config = {
  theme: {
    extend: {
      // Transition durations
      transitionDuration: {
        '0': '0ms',
        '75': '75ms',
        '100': '100ms',
        '150': '150ms',
        '200': '200ms',
        '300': '300ms',
        '500': '500ms',
        '700': '700ms',
        '1000': '1000ms',
      },

      // Keyframes
      keyframes: {
        // Fade animations
        'fade-in': {
          from: { opacity: '0' },
          to: { opacity: '1' },
        },
        'fade-out': {
          from: { opacity: '1' },
          to: { opacity: '0' },
        },

        // Scale animations
        'scale-in': {
          from: { opacity: '0', transform: 'scale(0.95)' },
          to: { opacity: '1', transform: 'scale(1)' },
        },
        'scale-out': {
          from: { opacity: '1', transform: 'scale(1)' },
          to: { opacity: '0', transform: 'scale(0.95)' },
        },

        // Slide animations
        'slide-in-from-top': {
          from: { opacity: '0', transform: 'translateY(-10px)' },
          to: { opacity: '1', transform: 'translateY(0)' },
        },
        'slide-in-from-bottom': {
          from: { opacity: '0', transform: 'translateY(10px)' },
          to: { opacity: '1', transform: 'translateY(0)' },
        },
        'slide-in-from-left': {
          from: { opacity: '0', transform: 'translateX(-10px)' },
          to: { opacity: '1', transform: 'translateX(0)' },
        },
        'slide-in-from-right': {
          from: { opacity: '0', transform: 'translateX(10px)' },
          to: { opacity: '1', transform: 'translateX(0)' },
        },
        'slide-out-to-top': {
          from: { opacity: '1', transform: 'translateY(0)' },
          to: { opacity: '0', transform: 'translateY(-10px)' },
        },
        'slide-out-to-bottom': {
          from: { opacity: '1', transform: 'translateY(0)' },
          to: { opacity: '0', transform: 'translateY(10px)' },
        },

        // Loading animations
        'spin': {
          from: { transform: 'rotate(0deg)' },
          to: { transform: 'rotate(360deg)' },
        },
        'pulse': {
          '0%, 100%': { opacity: '1' },
          '50%': { opacity: '0.5' },
        },
        'shimmer': {
          '0%': { transform: 'translateX(-100%)' },
          '100%': { transform: 'translateX(100%)' },
        },
        'bounce': {
          '0%, 100%': {
            transform: 'translateY(-25%)',
            animationTimingFunction: 'cubic-bezier(0.8, 0, 1, 1)',
          },
          '50%': {
            transform: 'translateY(0)',
            animationTimingFunction: 'cubic-bezier(0, 0, 0.2, 1)',
          },
        },

        // Micro-interactions
        'ping': {
          '75%, 100%': { transform: 'scale(2)', opacity: '0' },
        },
        'wiggle': {
          '0%, 100%': { transform: 'rotate(-3deg)' },
          '50%': { transform: 'rotate(3deg)' },
        },

        // Accordion/Collapse
        'accordion-down': {
          from: { height: '0', opacity: '0' },
          to: { height: 'var(--radix-accordion-content-height)', opacity: '1' },
        },
        'accordion-up': {
          from: { height: 'var(--radix-accordion-content-height)', opacity: '1' },
          to: { height: '0', opacity: '0' },
        },

        // Collapsible
        'collapsible-down': {
          from: { height: '0' },
          to: { height: 'var(--radix-collapsible-content-height)' },
        },
        'collapsible-up': {
          from: { height: 'var(--radix-collapsible-content-height)' },
          to: { height: '0' },
        },
      },

      // Animation utilities
      animation: {
        // Fade
        'fade-in': 'fade-in 200ms ease-out',
        'fade-out': 'fade-out 150ms ease-in',

        // Scale
        'scale-in': 'scale-in 200ms ease-out',
        'scale-out': 'scale-out 150ms ease-in',

        // Slide
        'slide-in-from-top': 'slide-in-from-top 200ms ease-out',
        'slide-in-from-bottom': 'slide-in-from-bottom 200ms ease-out',
        'slide-in-from-left': 'slide-in-from-left 200ms ease-out',
        'slide-in-from-right': 'slide-in-from-right 200ms ease-out',
        'slide-out-to-top': 'slide-out-to-top 150ms ease-in',
        'slide-out-to-bottom': 'slide-out-to-bottom 150ms ease-in',

        // Loading
        'spin': 'spin 1s linear infinite',
        'pulse': 'pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite',
        'shimmer': 'shimmer 1.5s infinite',
        'bounce': 'bounce 1s infinite',

        // Micro-interactions
        'ping': 'ping 1s cubic-bezier(0, 0, 0.2, 1) infinite',
        'wiggle': 'wiggle 0.3s ease-in-out',

        // Accordion
        'accordion-down': 'accordion-down 200ms ease-out',
        'accordion-up': 'accordion-up 150ms ease-in',

        // Collapsible
        'collapsible-down': 'collapsible-down 200ms ease-out',
        'collapsible-up': 'collapsible-up 150ms ease-in',
      },
    },
  },
}

export default config
```

---

## 2. Hover Effects

### Scale Hover

For interactive cards and clickable elements:

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const hoverScaleVariants = cva(
  'transition-transform duration-150 ease-out',
  {
    variants: {
      scale: {
        subtle: 'hover:scale-[1.02]',
        medium: 'hover:scale-105',
        prominent: 'hover:scale-110',
      },
    },
    defaultVariants: {
      scale: 'subtle',
    },
  }
)

// Usage
<div className={hoverScaleVariants({ scale: 'subtle' })}>
  Hoverable card
</div>
```

### Color Transitions

```tsx
// Background color transition
<button className="
  bg-gray-900 hover:bg-gray-800
  transition-colors duration-150
">
  Solid Button
</button>

// Text color transition
<a className="
  text-gray-600 hover:text-gray-900
  transition-colors duration-150
">
  Link text
</a>

// Border color transition
<input className="
  border-gray-300 focus:border-mereka-teal
  transition-colors duration-150
"/>

// Multi-property color transition
<button className="
  bg-transparent text-gray-900 border border-gray-300
  hover:bg-gray-100 hover:border-gray-400
  transition-colors duration-150
">
  Outline Button
</button>
```

### Shadow Transitions

```tsx
// Card hover shadow lift
<article className="
  bg-white rounded-xl shadow-md
  hover:shadow-lg
  transition-shadow duration-200
">
  Card content
</article>

// Combined lift effect (scale + shadow)
<article className="
  bg-white rounded-xl shadow-md
  hover:shadow-xl hover:scale-[1.02]
  transition-all duration-200
">
  Interactive card
</article>
```

### Complete Hover Effect Examples

```tsx
// Experience Card Hover
function ExperienceCard({ experience }: { experience: Experience }) {
  return (
    <article className="
      bg-white rounded-xl overflow-hidden shadow-md
      hover:shadow-xl hover:scale-[1.02]
      transition-all duration-200 ease-out
      cursor-pointer
    ">
      {/* Image with zoom on hover */}
      <div className="aspect-[3/2] overflow-hidden">
        <img
          src={experience.image}
          alt={experience.title}
          className="
            w-full h-full object-cover
            group-hover:scale-105
            transition-transform duration-300 ease-out
          "
        />
      </div>

      <div className="p-4">
        <h3 className="font-heading text-h5">{experience.title}</h3>
        <p className="text-body-sm text-gray-600">{experience.description}</p>
      </div>
    </article>
  )
}

// Button with Icon Slide
function ButtonWithIconSlide({ children }: { children: React.ReactNode }) {
  return (
    <button className="
      group inline-flex items-center gap-2
      bg-mereka-teal text-white rounded-full px-6 py-3
      hover:bg-mereka-teal/90
      transition-colors duration-150
    ">
      {children}
      <ArrowRightIcon className="
        w-4 h-4
        group-hover:translate-x-1
        transition-transform duration-150
      " />
    </button>
  )
}

// Link with Underline Animation
function AnimatedLink({ href, children }: { href: string; children: React.ReactNode }) {
  return (
    <a
      href={href}
      className="
        relative text-mereka-teal
        after:absolute after:left-0 after:bottom-0
        after:h-0.5 after:w-0 after:bg-mereka-teal
        hover:after:w-full
        after:transition-all after:duration-200
      "
    >
      {children}
    </a>
  )
}
```

---

## 3. Page Transitions

### Fade Transition

Simple, universal page transition:

```tsx
// Using Framer Motion
import { motion, AnimatePresence } from 'framer-motion'

const pageVariants = {
  initial: { opacity: 0 },
  enter: { opacity: 1 },
  exit: { opacity: 0 },
}

const pageTransition = {
  duration: 0.3,
  ease: 'easeOut',
}

function PageWrapper({ children }: { children: React.ReactNode }) {
  return (
    <motion.div
      initial="initial"
      animate="enter"
      exit="exit"
      variants={pageVariants}
      transition={pageTransition}
    >
      {children}
    </motion.div>
  )
}

// In your router
function App() {
  return (
    <AnimatePresence mode="wait">
      <Routes location={location} key={location.pathname}>
        <Route
          path="/"
          element={
            <PageWrapper>
              <HomePage />
            </PageWrapper>
          }
        />
        {/* More routes */}
      </Routes>
    </AnimatePresence>
  )
}
```

### Slide Transition

For hierarchical navigation (forward/back):

```tsx
const slideVariants = {
  initial: (direction: number) => ({
    x: direction > 0 ? '100%' : '-100%',
    opacity: 0,
  }),
  enter: {
    x: 0,
    opacity: 1,
  },
  exit: (direction: number) => ({
    x: direction < 0 ? '100%' : '-100%',
    opacity: 0,
  }),
}

const slideTransition = {
  type: 'tween',
  duration: 0.3,
  ease: [0.4, 0, 0.2, 1],
}

interface SlidePageProps {
  children: React.ReactNode
  direction: number // 1 for forward, -1 for back
}

function SlidePage({ children, direction }: SlidePageProps) {
  return (
    <motion.div
      custom={direction}
      initial="initial"
      animate="enter"
      exit="exit"
      variants={slideVariants}
      transition={slideTransition}
      className="absolute inset-0"
    >
      {children}
    </motion.div>
  )
}
```

### CSS-Only Page Transitions

For simpler setups without Framer Motion:

```tsx
// Using View Transitions API (modern browsers)
function navigateWithTransition(href: string) {
  if (!document.startViewTransition) {
    window.location.href = href
    return
  }

  document.startViewTransition(() => {
    window.location.href = href
  })
}

// CSS for View Transitions
const viewTransitionStyles = `
  ::view-transition-old(root) {
    animation: fade-out 150ms ease-in;
  }

  ::view-transition-new(root) {
    animation: fade-in 200ms ease-out;
  }

  @keyframes fade-in {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  @keyframes fade-out {
    from { opacity: 1; }
    to { opacity: 0; }
  }
`

// Using CSS classes
function PageTransition({ children }: { children: React.ReactNode }) {
  return (
    <main className="animate-fade-in">
      {children}
    </main>
  )
}
```

---

## 4. Component Animations

### Modal Open/Close

```tsx
import * as DialogPrimitive from '@radix-ui/react-dialog'

// Overlay animation
const overlayVariants = {
  hidden: { opacity: 0 },
  visible: { opacity: 1 },
}

// Content animation
const contentVariants = {
  hidden: {
    opacity: 0,
    scale: 0.95,
    y: -10,
  },
  visible: {
    opacity: 1,
    scale: 1,
    y: 0,
  },
}

function AnimatedModal({
  open,
  onOpenChange,
  children,
}: {
  open: boolean
  onOpenChange: (open: boolean) => void
  children: React.ReactNode
}) {
  return (
    <DialogPrimitive.Root open={open} onOpenChange={onOpenChange}>
      <AnimatePresence>
        {open && (
          <DialogPrimitive.Portal forceMount>
            {/* Overlay */}
            <DialogPrimitive.Overlay asChild>
              <motion.div
                className="fixed inset-0 z-50 bg-black/50 backdrop-blur-sm"
                initial="hidden"
                animate="visible"
                exit="hidden"
                variants={overlayVariants}
                transition={{ duration: 0.2 }}
              />
            </DialogPrimitive.Overlay>

            {/* Content */}
            <DialogPrimitive.Content asChild>
              <motion.div
                className="fixed left-1/2 top-1/2 z-50 w-full max-w-md bg-white rounded-xl shadow-xl"
                initial="hidden"
                animate="visible"
                exit="hidden"
                variants={contentVariants}
                transition={{ duration: 0.2, ease: [0, 0, 0.2, 1] }}
                style={{ x: '-50%', y: '-50%' }}
              >
                {children}
              </motion.div>
            </DialogPrimitive.Content>
          </DialogPrimitive.Portal>
        )}
      </AnimatePresence>
    </DialogPrimitive.Root>
  )
}

// Tailwind-only version (using data attributes)
function TailwindModal({ children }: { children: React.ReactNode }) {
  return (
    <DialogPrimitive.Portal>
      <DialogPrimitive.Overlay className="
        fixed inset-0 z-50 bg-black/50 backdrop-blur-sm
        data-[state=open]:animate-fade-in
        data-[state=closed]:animate-fade-out
      " />
      <DialogPrimitive.Content className="
        fixed left-1/2 top-1/2 z-50 -translate-x-1/2 -translate-y-1/2
        w-full max-w-md bg-white rounded-xl shadow-xl
        data-[state=open]:animate-scale-in
        data-[state=closed]:animate-scale-out
      ">
        {children}
      </DialogPrimitive.Content>
    </DialogPrimitive.Portal>
  )
}
```

---

### Dropdown Expand/Collapse

```tsx
import * as DropdownMenuPrimitive from '@radix-ui/react-dropdown-menu'

function AnimatedDropdown({
  trigger,
  children,
}: {
  trigger: React.ReactNode
  children: React.ReactNode
}) {
  return (
    <DropdownMenuPrimitive.Root>
      <DropdownMenuPrimitive.Trigger asChild>
        {trigger}
      </DropdownMenuPrimitive.Trigger>

      <DropdownMenuPrimitive.Portal>
        <DropdownMenuPrimitive.Content
          sideOffset={4}
          className="
            z-50 min-w-[180px] overflow-hidden rounded-lg
            bg-white shadow-lg border border-gray-200

            data-[state=open]:animate-in
            data-[state=closed]:animate-out
            data-[state=closed]:fade-out-0
            data-[state=open]:fade-in-0
            data-[state=closed]:zoom-out-95
            data-[state=open]:zoom-in-95
            data-[side=bottom]:slide-in-from-top-2
            data-[side=left]:slide-in-from-right-2
            data-[side=right]:slide-in-from-left-2
            data-[side=top]:slide-in-from-bottom-2
          "
        >
          {children}
        </DropdownMenuPrimitive.Content>
      </DropdownMenuPrimitive.Portal>
    </DropdownMenuPrimitive.Root>
  )
}

// Dropdown item with hover animation
function DropdownItem({ children, ...props }: DropdownMenuPrimitive.DropdownMenuItemProps) {
  return (
    <DropdownMenuPrimitive.Item
      className="
        flex items-center gap-2 px-3 py-2
        text-body text-gray-700
        outline-none cursor-pointer
        transition-colors duration-150
        data-[highlighted]:bg-gray-100
        data-[highlighted]:text-gray-900
      "
      {...props}
    >
      {children}
    </DropdownMenuPrimitive.Item>
  )
}
```

---

### Accordion Expand/Collapse

```tsx
import * as AccordionPrimitive from '@radix-ui/react-accordion'
import { ChevronDown } from 'lucide-react'

function Accordion({ items }: { items: Array<{ title: string; content: string }> }) {
  return (
    <AccordionPrimitive.Root type="single" collapsible className="space-y-2">
      {items.map((item, index) => (
        <AccordionPrimitive.Item
          key={index}
          value={`item-${index}`}
          className="bg-white rounded-lg border border-gray-200 overflow-hidden"
        >
          <AccordionPrimitive.Header>
            <AccordionPrimitive.Trigger className="
              flex items-center justify-between w-full p-4
              text-left font-medium text-gray-900
              hover:bg-gray-50 transition-colors duration-150
              group
            ">
              {item.title}
              <ChevronDown className="
                w-5 h-5 text-gray-400
                transition-transform duration-200
                group-data-[state=open]:rotate-180
              " />
            </AccordionPrimitive.Trigger>
          </AccordionPrimitive.Header>

          <AccordionPrimitive.Content className="
            overflow-hidden
            data-[state=open]:animate-accordion-down
            data-[state=closed]:animate-accordion-up
          ">
            <div className="p-4 pt-0 text-body text-gray-600">
              {item.content}
            </div>
          </AccordionPrimitive.Content>
        </AccordionPrimitive.Item>
      ))}
    </AccordionPrimitive.Root>
  )
}
```

---

### Toast Enter/Exit

```tsx
import * as ToastPrimitive from '@radix-ui/react-toast'
import { cva, type VariantProps } from 'class-variance-authority'
import { CheckCircle, XCircle, AlertTriangle, Info, X } from 'lucide-react'

const toastVariants = cva(
  `
    relative flex items-start gap-3 p-4 rounded-xl shadow-lg
    data-[state=open]:animate-in
    data-[state=closed]:animate-out
    data-[swipe=end]:animate-out
    data-[state=closed]:fade-out-80
    data-[state=closed]:slide-out-to-right-full
    data-[state=open]:slide-in-from-top-full
    data-[state=open]:sm:slide-in-from-bottom-full
  `,
  {
    variants: {
      variant: {
        success: 'bg-green-50 border border-green-200',
        error: 'bg-red-50 border border-red-200',
        warning: 'bg-yellow-50 border border-yellow-200',
        info: 'bg-blue-50 border border-blue-200',
      },
    },
    defaultVariants: {
      variant: 'info',
    },
  }
)

interface ToastProps extends VariantProps<typeof toastVariants> {
  title: string
  description?: string
  onClose?: () => void
}

function Toast({ variant, title, description, onClose }: ToastProps) {
  const icons = {
    success: <CheckCircle className="w-5 h-5 text-mereka-forest" />,
    error: <XCircle className="w-5 h-5 text-mereka-burgundy" />,
    warning: <AlertTriangle className="w-5 h-5 text-mereka-orange" />,
    info: <Info className="w-5 h-5 text-mereka-blue" />,
  }

  return (
    <ToastPrimitive.Root className={toastVariants({ variant })}>
      {icons[variant || 'info']}

      <div className="flex-1">
        <ToastPrimitive.Title className="font-medium text-gray-900">
          {title}
        </ToastPrimitive.Title>
        {description && (
          <ToastPrimitive.Description className="text-body-sm text-gray-600 mt-1">
            {description}
          </ToastPrimitive.Description>
        )}
      </div>

      <ToastPrimitive.Close
        onClick={onClose}
        className="p-1 rounded-full hover:bg-black/5 transition-colors duration-150"
      >
        <X className="w-4 h-4 text-gray-400" />
      </ToastPrimitive.Close>
    </ToastPrimitive.Root>
  )
}

// Toast viewport (container)
function ToastViewport() {
  return (
    <ToastPrimitive.Viewport className="
      fixed top-0 z-[100] flex max-h-screen w-full flex-col-reverse p-4
      sm:bottom-0 sm:right-0 sm:top-auto sm:flex-col md:max-w-[420px]
    " />
  )
}
```

---

### Card Hover Lift

```tsx
// Basic lift effect
function LiftCard({ children }: { children: React.ReactNode }) {
  return (
    <div className="
      bg-white rounded-xl p-6 shadow-md
      transform transition-all duration-200 ease-out
      hover:shadow-xl hover:-translate-y-1
    ">
      {children}
    </div>
  )
}

// Experience card with image zoom + lift
function ExperienceCardAnimated({ experience }: { experience: Experience }) {
  return (
    <article className="
      group bg-white rounded-xl overflow-hidden shadow-md
      transform transition-all duration-200 ease-out
      hover:shadow-xl hover:-translate-y-1
    ">
      {/* Image with zoom */}
      <div className="aspect-[3/2] overflow-hidden">
        <img
          src={experience.image}
          alt={experience.title}
          className="
            w-full h-full object-cover
            transition-transform duration-300 ease-out
            group-hover:scale-105
          "
        />
      </div>

      {/* Content */}
      <div className="p-4">
        <h3 className="font-heading text-h5 text-gray-900">
          {experience.title}
        </h3>
        <p className="text-body-sm text-gray-600 mt-1">
          {experience.description}
        </p>

        {/* Price with subtle animation */}
        <div className="
          mt-4 flex items-baseline gap-1
          transition-colors duration-150
          group-hover:text-mereka-teal
        ">
          <span className="text-caption text-gray-400">From</span>
          <span className="font-heading text-h4">
            RM {experience.price}
          </span>
        </div>
      </div>
    </article>
  )
}
```

---

## 5. Loading Animations

### Spinner

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const spinnerVariants = cva(
  'animate-spin rounded-full border-2 border-current border-t-transparent',
  {
    variants: {
      size: {
        xs: 'w-3 h-3',
        sm: 'w-4 h-4',
        md: 'w-6 h-6',
        lg: 'w-8 h-8',
        xl: 'w-12 h-12',
      },
      color: {
        primary: 'text-mereka-teal',
        white: 'text-white',
        gray: 'text-gray-400',
        current: 'text-current',
      },
    },
    defaultVariants: {
      size: 'md',
      color: 'primary',
    },
  }
)

interface SpinnerProps extends VariantProps<typeof spinnerVariants> {
  className?: string
  label?: string
}

function Spinner({ size, color, className, label = 'Loading...' }: SpinnerProps) {
  return (
    <div role="status" aria-label={label}>
      <div className={spinnerVariants({ size, color, className })} />
      <span className="sr-only">{label}</span>
    </div>
  )
}

// Usage
<Spinner size="md" color="primary" />
<Spinner size="sm" color="white" /> {/* For buttons */}
<Spinner size="xl" color="gray" />  {/* For page loading */}
```

---

### Pulse

```tsx
// Simple pulse skeleton
function PulseSkeleton({ className }: { className?: string }) {
  return (
    <div className={`bg-gray-200 rounded animate-pulse ${className}`} />
  )
}

// Text skeleton
function TextSkeleton({
  width = 'full',
  height = 'body',
}: {
  width?: 'full' | '3/4' | '1/2' | '1/3' | '1/4'
  height?: 'title' | 'body' | 'caption'
}) {
  const widthClasses = {
    full: 'w-full',
    '3/4': 'w-3/4',
    '1/2': 'w-1/2',
    '1/3': 'w-1/3',
    '1/4': 'w-1/4',
  }

  const heightClasses = {
    title: 'h-7',
    body: 'h-4',
    caption: 'h-3',
  }

  return (
    <PulseSkeleton className={`${widthClasses[width]} ${heightClasses[height]}`} />
  )
}

// Card skeleton with pulse
function CardSkeleton() {
  return (
    <div className="bg-white rounded-xl overflow-hidden shadow-md">
      {/* Image placeholder */}
      <PulseSkeleton className="aspect-[3/2]" />

      {/* Content */}
      <div className="p-4 space-y-3">
        <TextSkeleton width="1/3" height="caption" />
        <TextSkeleton width="full" height="title" />
        <TextSkeleton width="3/4" height="body" />
        <div className="flex items-center gap-2 pt-2">
          <PulseSkeleton className="w-6 h-6 rounded-full" />
          <TextSkeleton width="1/2" height="caption" />
        </div>
      </div>
    </div>
  )
}
```

---

### Shimmer

```tsx
// Shimmer effect component
function Shimmer({ className }: { className?: string }) {
  return (
    <div className={`
      relative overflow-hidden bg-gray-200 rounded
      before:absolute before:inset-0 before:-translate-x-full
      before:animate-shimmer
      before:bg-gradient-to-r
      before:from-transparent before:via-white/60 before:to-transparent
      ${className}
    `} />
  )
}

// Text with shimmer
function ShimmerText({ width = 'full' }: { width?: string }) {
  return <Shimmer className={`h-4 ${width}`} />
}

// Card with shimmer effect
function ShimmerCard() {
  return (
    <div className="bg-white rounded-xl overflow-hidden shadow-md">
      {/* Image placeholder with shimmer */}
      <Shimmer className="aspect-[3/2]" />

      {/* Content */}
      <div className="p-4 space-y-3">
        <Shimmer className="h-3 w-16" />
        <Shimmer className="h-6 w-full" />
        <Shimmer className="h-4 w-3/4" />
        <div className="flex items-center gap-2 pt-2">
          <Shimmer className="w-8 h-8 rounded-full" />
          <Shimmer className="h-3 w-24" />
        </div>
      </div>
    </div>
  )
}

// Shimmer table rows
function ShimmerTableRow({ columns }: { columns: number }) {
  return (
    <tr className="border-b border-gray-100">
      {Array.from({ length: columns }).map((_, i) => (
        <td key={i} className="py-4 px-4">
          <Shimmer className="h-4 w-full" />
        </td>
      ))}
    </tr>
  )
}
```

---

### Dot Loader

For inline loading indicators:

```tsx
function DotLoader() {
  return (
    <div className="flex items-center gap-1" role="status" aria-label="Loading">
      {[0, 1, 2].map((i) => (
        <span
          key={i}
          className="w-2 h-2 bg-gray-400 rounded-full animate-bounce"
          style={{ animationDelay: `${i * 0.15}s` }}
        />
      ))}
      <span className="sr-only">Loading...</span>
    </div>
  )
}

// Usage in chat/messaging
function TypingIndicator() {
  return (
    <div className="flex items-center gap-2 p-3 bg-gray-100 rounded-lg w-fit">
      <span className="text-body-sm text-gray-500">Someone is typing</span>
      <DotLoader />
    </div>
  )
}
```

---

## 6. Micro-Interactions

### Button Press

```tsx
function PressableButton({ children, ...props }: React.ButtonHTMLAttributes<HTMLButtonElement>) {
  return (
    <button
      className="
        bg-gray-900 text-white rounded-full px-6 py-3 font-medium
        transition-all duration-75
        hover:bg-gray-800
        active:scale-95
        focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2
      "
      {...props}
    >
      {children}
    </button>
  )
}

// With loading state
function LoadingButton({
  children,
  isLoading,
  ...props
}: React.ButtonHTMLAttributes<HTMLButtonElement> & { isLoading?: boolean }) {
  return (
    <button
      className="
        relative bg-gray-900 text-white rounded-full px-6 py-3 font-medium
        transition-all duration-75
        hover:bg-gray-800
        active:scale-95
        disabled:opacity-70 disabled:cursor-not-allowed
      "
      disabled={isLoading}
      {...props}
    >
      <span className={isLoading ? 'invisible' : ''}>{children}</span>
      {isLoading && (
        <span className="absolute inset-0 flex items-center justify-center">
          <Spinner size="sm" color="white" />
        </span>
      )}
    </button>
  )
}
```

---

### Checkbox Check

```tsx
import * as CheckboxPrimitive from '@radix-ui/react-checkbox'
import { Check } from 'lucide-react'

function AnimatedCheckbox({
  checked,
  onCheckedChange,
  label,
}: {
  checked: boolean
  onCheckedChange: (checked: boolean) => void
  label: string
}) {
  return (
    <label className="flex items-center gap-3 cursor-pointer group">
      <CheckboxPrimitive.Root
        checked={checked}
        onCheckedChange={onCheckedChange}
        className="
          w-5 h-5 rounded border-2 border-gray-300
          flex items-center justify-center
          transition-all duration-150
          data-[state=checked]:bg-mereka-teal
          data-[state=checked]:border-mereka-teal
          focus:outline-none focus:ring-2 focus:ring-mereka-teal/20
          group-hover:border-gray-400
        "
      >
        <CheckboxPrimitive.Indicator className="text-white">
          <Check className="
            w-3.5 h-3.5
            animate-in zoom-in-50 duration-150
          " />
        </CheckboxPrimitive.Indicator>
      </CheckboxPrimitive.Root>

      <span className="text-body text-gray-700 select-none">
        {label}
      </span>
    </label>
  )
}
```

---

### Toggle/Switch Flip

```tsx
import * as SwitchPrimitive from '@radix-ui/react-switch'

function AnimatedSwitch({
  checked,
  onCheckedChange,
  label,
}: {
  checked: boolean
  onCheckedChange: (checked: boolean) => void
  label?: string
}) {
  return (
    <div className="flex items-center gap-3">
      <SwitchPrimitive.Root
        checked={checked}
        onCheckedChange={onCheckedChange}
        className="
          relative w-11 h-6 rounded-full
          bg-gray-200 data-[state=checked]:bg-mereka-teal
          transition-colors duration-200
          focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:ring-offset-2
        "
      >
        <SwitchPrimitive.Thumb className="
          block w-5 h-5 rounded-full bg-white shadow-md
          transition-transform duration-200 ease-out
          translate-x-0.5 data-[state=checked]:translate-x-[22px]
        " />
      </SwitchPrimitive.Root>

      {label && (
        <span className="text-body text-gray-700">{label}</span>
      )}
    </div>
  )
}
```

---

### Like/Heart Animation

```tsx
import { useState } from 'react'
import { Heart } from 'lucide-react'

function LikeButton({ initialLiked = false }: { initialLiked?: boolean }) {
  const [liked, setLiked] = useState(initialLiked)
  const [animating, setAnimating] = useState(false)

  const handleClick = () => {
    if (!liked) {
      setAnimating(true)
      setTimeout(() => setAnimating(false), 300)
    }
    setLiked(!liked)
  }

  return (
    <button
      onClick={handleClick}
      className="
        relative p-2 rounded-full
        transition-colors duration-150
        hover:bg-gray-100
        focus:outline-none focus:ring-2 focus:ring-mereka-teal/20
      "
      aria-label={liked ? 'Unlike' : 'Like'}
    >
      <Heart
        className={`
          w-6 h-6 transition-all duration-200
          ${liked
            ? 'fill-red-500 text-red-500 scale-110'
            : 'text-gray-400 hover:text-gray-600'
          }
          ${animating ? 'animate-ping' : ''}
        `}
      />

      {/* Ping effect on like */}
      {animating && (
        <span className="
          absolute inset-0 flex items-center justify-center
        ">
          <Heart className="
            w-6 h-6 text-red-500 fill-red-500
            animate-ping opacity-75
          " />
        </span>
      )}
    </button>
  )
}
```

---

### Ripple Effect

```tsx
import { useState, useRef } from 'react'

interface RippleProps {
  x: number
  y: number
  size: number
}

function RippleButton({ children, ...props }: React.ButtonHTMLAttributes<HTMLButtonElement>) {
  const [ripples, setRipples] = useState<RippleProps[]>([])
  const buttonRef = useRef<HTMLButtonElement>(null)

  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    const button = buttonRef.current
    if (!button) return

    const rect = button.getBoundingClientRect()
    const size = Math.max(rect.width, rect.height)
    const x = e.clientX - rect.left - size / 2
    const y = e.clientY - rect.top - size / 2

    const newRipple = { x, y, size }
    setRipples([...ripples, newRipple])

    // Remove ripple after animation
    setTimeout(() => {
      setRipples((current) => current.slice(1))
    }, 600)

    props.onClick?.(e)
  }

  return (
    <button
      ref={buttonRef}
      className="
        relative overflow-hidden
        bg-gray-900 text-white rounded-full px-6 py-3 font-medium
        hover:bg-gray-800
        focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2
      "
      onClick={handleClick}
      {...props}
    >
      {children}

      {/* Ripple elements */}
      {ripples.map((ripple, index) => (
        <span
          key={index}
          className="
            absolute rounded-full bg-white/30
            animate-[ripple_0.6s_linear]
            pointer-events-none
          "
          style={{
            left: ripple.x,
            top: ripple.y,
            width: ripple.size,
            height: ripple.size,
          }}
        />
      ))}
    </button>
  )
}

// Add to tailwind.config.ts keyframes:
// 'ripple': {
//   'from': { transform: 'scale(0)', opacity: '1' },
//   'to': { transform: 'scale(4)', opacity: '0' },
// }
```

---

## 7. Scroll Animations

### Fade In On Scroll

```tsx
import { useEffect, useRef, useState } from 'react'

function useInView(options?: IntersectionObserverInit) {
  const ref = useRef<HTMLElement>(null)
  const [inView, setInView] = useState(false)

  useEffect(() => {
    const element = ref.current
    if (!element) return

    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          setInView(true)
          observer.unobserve(element) // Only trigger once
        }
      },
      { threshold: 0.1, ...options }
    )

    observer.observe(element)
    return () => observer.disconnect()
  }, [])

  return { ref, inView }
}

// Fade in component
function FadeInOnScroll({
  children,
  delay = 0,
}: {
  children: React.ReactNode
  delay?: number
}) {
  const { ref, inView } = useInView()

  return (
    <div
      ref={ref as React.RefObject<HTMLDivElement>}
      className={`
        transition-all duration-500 ease-out
        ${inView
          ? 'opacity-100 translate-y-0'
          : 'opacity-0 translate-y-8'
        }
      `}
      style={{ transitionDelay: `${delay}ms` }}
    >
      {children}
    </div>
  )
}

// Staggered fade in for lists
function StaggeredFadeIn({
  children,
  staggerDelay = 100,
}: {
  children: React.ReactNode[]
  staggerDelay?: number
}) {
  return (
    <>
      {children.map((child, index) => (
        <FadeInOnScroll key={index} delay={index * staggerDelay}>
          {child}
        </FadeInOnScroll>
      ))}
    </>
  )
}

// Usage
function FeatureSection() {
  const features = [
    { title: 'Feature 1', description: '...' },
    { title: 'Feature 2', description: '...' },
    { title: 'Feature 3', description: '...' },
  ]

  return (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
      <StaggeredFadeIn staggerDelay={100}>
        {features.map((feature) => (
          <FeatureCard key={feature.title} {...feature} />
        ))}
      </StaggeredFadeIn>
    </div>
  )
}
```

---

### Slide In Variations

```tsx
type SlideDirection = 'up' | 'down' | 'left' | 'right'

function SlideInOnScroll({
  children,
  direction = 'up',
  delay = 0,
}: {
  children: React.ReactNode
  direction?: SlideDirection
  delay?: number
}) {
  const { ref, inView } = useInView()

  const directionClasses = {
    up: inView ? 'translate-y-0' : 'translate-y-12',
    down: inView ? 'translate-y-0' : '-translate-y-12',
    left: inView ? 'translate-x-0' : 'translate-x-12',
    right: inView ? 'translate-x-0' : '-translate-x-12',
  }

  return (
    <div
      ref={ref as React.RefObject<HTMLDivElement>}
      className={`
        transition-all duration-700 ease-out
        ${inView ? 'opacity-100' : 'opacity-0'}
        ${directionClasses[direction]}
      `}
      style={{ transitionDelay: `${delay}ms` }}
    >
      {children}
    </div>
  )
}
```

---

### Parallax Scroll

```tsx
import { useEffect, useState } from 'react'

function useParallax(speed: number = 0.5) {
  const [offset, setOffset] = useState(0)

  useEffect(() => {
    const handleScroll = () => {
      setOffset(window.scrollY * speed)
    }

    window.addEventListener('scroll', handleScroll, { passive: true })
    return () => window.removeEventListener('scroll', handleScroll)
  }, [speed])

  return offset
}

function ParallaxImage({
  src,
  alt,
  speed = 0.3,
}: {
  src: string
  alt: string
  speed?: number
}) {
  const offset = useParallax(speed)

  return (
    <div className="overflow-hidden">
      <img
        src={src}
        alt={alt}
        className="w-full h-full object-cover"
        style={{ transform: `translateY(${offset}px)` }}
      />
    </div>
  )
}

// Hero with parallax
function ParallaxHero() {
  const offset = useParallax(0.4)

  return (
    <section className="relative h-[80vh] overflow-hidden">
      {/* Background with parallax */}
      <div
        className="absolute inset-0 bg-cover bg-center"
        style={{
          backgroundImage: 'url(/hero-bg.jpg)',
          transform: `translateY(${offset}px)`,
        }}
      />

      {/* Content */}
      <div className="relative z-10 flex items-center justify-center h-full">
        <h1 className="text-h1 text-white">Welcome to Mereka</h1>
      </div>
    </section>
  )
}
```

---

## 8. Reduced Motion Support

### Respecting User Preferences

Always respect the `prefers-reduced-motion` media query for users who have enabled reduced motion in their OS settings.

```tsx
// Hook to detect reduced motion preference
function usePrefersReducedMotion() {
  const [prefersReducedMotion, setPrefersReducedMotion] = useState(false)

  useEffect(() => {
    const mediaQuery = window.matchMedia('(prefers-reduced-motion: reduce)')
    setPrefersReducedMotion(mediaQuery.matches)

    const handler = (e: MediaQueryListEvent) => {
      setPrefersReducedMotion(e.matches)
    }

    mediaQuery.addEventListener('change', handler)
    return () => mediaQuery.removeEventListener('change', handler)
  }, [])

  return prefersReducedMotion
}

// Usage in components
function AnimatedCard({ children }: { children: React.ReactNode }) {
  const prefersReducedMotion = usePrefersReducedMotion()

  return (
    <div className={`
      bg-white rounded-xl shadow-md
      ${prefersReducedMotion
        ? '' // No animation
        : 'hover:shadow-xl hover:-translate-y-1 transition-all duration-200'
      }
    `}>
      {children}
    </div>
  )
}
```

---

### Tailwind Reduced Motion Utilities

Tailwind provides built-in utilities for reduced motion:

```tsx
// motion-safe: Only applies when user allows motion
<div className="motion-safe:animate-bounce">
  Bouncing element (respects preference)
</div>

// motion-reduce: Only applies when user prefers reduced motion
<div className="
  animate-spin
  motion-reduce:animate-none
">
  Spinner (stops for reduced motion)
</div>

// Combined example
<button className="
  bg-mereka-teal text-white rounded-full px-6 py-3

  motion-safe:transition-transform
  motion-safe:duration-150
  motion-safe:hover:scale-105
  motion-safe:active:scale-95

  motion-reduce:hover:opacity-80
">
  Interactive Button
</button>
```

---

### CSS-Based Reduced Motion

```css
/* Global reduced motion styles */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}

/* Alternative: Provide simplified animations instead of removing */
@media (prefers-reduced-motion: reduce) {
  .animate-slide-in {
    animation: fade-in 0.01ms ease-out;
  }

  .animate-bounce {
    animation: none;
  }

  .hover\:scale-105:hover {
    transform: none;
    opacity: 0.8;
  }
}
```

---

### Component Patterns for Accessibility

```tsx
// Animated component with reduced motion fallback
const fadeInVariants = {
  hidden: { opacity: 0, y: 20 },
  visible: { opacity: 1, y: 0 },
}

const reducedMotionVariants = {
  hidden: { opacity: 0 },
  visible: { opacity: 1 },
}

function AccessibleFadeIn({ children }: { children: React.ReactNode }) {
  const prefersReducedMotion = usePrefersReducedMotion()

  return (
    <motion.div
      initial="hidden"
      animate="visible"
      variants={prefersReducedMotion ? reducedMotionVariants : fadeInVariants}
      transition={{
        duration: prefersReducedMotion ? 0 : 0.3,
        ease: 'easeOut',
      }}
    >
      {children}
    </motion.div>
  )
}

// Loading spinner with reduced motion alternative
function AccessibleSpinner({ size = 'md' }: { size?: 'sm' | 'md' | 'lg' }) {
  const prefersReducedMotion = usePrefersReducedMotion()

  if (prefersReducedMotion) {
    // Show static loading indicator instead of spinning
    return (
      <div
        className={`
          rounded-full border-2 border-mereka-teal border-t-transparent
          ${size === 'sm' ? 'w-4 h-4' : size === 'lg' ? 'w-8 h-8' : 'w-6 h-6'}
        `}
        role="status"
        aria-label="Loading"
      >
        <span className="sr-only">Loading...</span>
      </div>
    )
  }

  return <Spinner size={size} color="primary" />
}

// Skeleton with optional animation
function AccessibleSkeleton({ className }: { className?: string }) {
  return (
    <div className={`
      bg-gray-200 rounded
      motion-safe:animate-pulse
      ${className}
    `} />
  )
}
```

---

## Animation Cheat Sheet

### Quick Reference

| Animation Type | Duration | Easing | Tailwind Classes |
|----------------|----------|--------|------------------|
| **Hover color** | 150ms | ease-out | `transition-colors duration-150` |
| **Hover scale** | 150ms | ease-out | `transition-transform duration-150 hover:scale-105` |
| **Button press** | 75ms | ease-out | `transition-transform duration-75 active:scale-95` |
| **Focus ring** | 100ms | ease-out | `transition-shadow duration-100` |
| **Dropdown open** | 200ms | ease-out | `animate-in fade-in zoom-in-95 duration-200` |
| **Dropdown close** | 150ms | ease-in | `animate-out fade-out zoom-out-95 duration-150` |
| **Modal open** | 200ms | ease-out | `animate-in fade-in zoom-in-95 duration-200` |
| **Modal close** | 150ms | ease-in | `animate-out fade-out zoom-out-95 duration-150` |
| **Toast enter** | 300ms | ease-out | `animate-in slide-in-from-top fade-in duration-300` |
| **Toast exit** | 200ms | ease-in | `animate-out slide-out-to-right fade-out duration-200` |
| **Accordion** | 200ms | ease-out | `animate-accordion-down` / `animate-accordion-up` |
| **Card lift** | 200ms | ease-out | `transition-all duration-200 hover:-translate-y-1 hover:shadow-xl` |
| **Spinner** | 1000ms | linear | `animate-spin` |
| **Pulse** | 2000ms | ease-in-out | `animate-pulse` |
| **Shimmer** | 1500ms | linear | `animate-shimmer` |
| **Page fade** | 300ms | ease-out | `animate-fade-in duration-300` |

---

## Do's and Don'ts

### Do's

- **Do** use consistent timing across similar interactions
- **Do** prefer `ease-out` for entering animations, `ease-in` for exiting
- **Do** keep animations under 500ms for responsiveness
- **Do** provide visual feedback for all interactive elements
- **Do** respect `prefers-reduced-motion` preferences
- **Do** use `will-change` sparingly for performance optimization
- **Do** test animations on lower-end devices
- **Do** use hardware-accelerated properties (transform, opacity)

### Don'ts

- **Don't** animate layout properties (width, height, top, left) when possible
- **Don't** use animations that block user interaction
- **Don't** create infinite animations without a purpose
- **Don't** use jarring or unexpected animations
- **Don't** animate too many elements simultaneously
- **Don't** use animations longer than 500ms for UI feedback
- **Don't** forget to provide loading states for async actions
- **Don't** skip exit animations - they help with orientation

---

## Performance Tips

### Hardware Acceleration

```tsx
// Prefer transform and opacity for smooth animations
<div className="transform translate-x-0 opacity-100 transition-all">
  ✓ Hardware accelerated
</div>

// Avoid animating layout properties
<div className="w-full transition-[width]">
  ✗ Causes layout recalculation
</div>
```

### Will-Change Optimization

```tsx
// Add will-change before animation starts
function HoverCard({ children }: { children: React.ReactNode }) {
  const [isHovered, setIsHovered] = useState(false)

  return (
    <div
      className={`
        transition-transform duration-200
        ${isHovered ? 'will-change-transform' : ''}
      `}
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
    >
      {children}
    </div>
  )
}
```

### Reducing Repaints

```tsx
// Use CSS containment for complex animated components
<div className="contain-layout contain-paint">
  <AnimatedContent />
</div>
```

---

## Related Documentation

- [Loading States](./loading.md) - Skeleton and spinner components
- [Dialogs](./dialogs.md) - Modal animation patterns
- [Buttons](./buttons.md) - Button interaction states
- [Notifications](./notifications.md) - Toast animation patterns
- [Cards](./cards.md) - Card hover effects
