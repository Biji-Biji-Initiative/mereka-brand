# Accessibility

> Comprehensive accessibility guidelines for Mereka. Covers WCAG compliance, keyboard navigation, screen reader support, focus management, and testing strategies.

**WCAG Target**: AA compliance minimum, AAA where feasible

---

## Table of Contents

1. [Color Contrast](#1-color-contrast)
2. [Keyboard Navigation](#2-keyboard-navigation)
3. [Screen Readers](#3-screen-readers)
4. [Focus Management](#4-focus-management)
5. [Form Accessibility](#5-form-accessibility)
6. [Image Accessibility](#6-image-accessibility)
7. [Motion and Animation](#7-motion-and-animation)
8. [Touch Targets](#8-touch-targets)
9. [Testing Tools](#9-testing-tools)

---

## 1. Color Contrast

### WCAG Requirements

| Level | Normal Text | Large Text | UI Components |
|-------|-------------|------------|---------------|
| **AA** | 4.5:1 | 3:1 | 3:1 |
| **AAA** | 7:1 | 4.5:1 | N/A |

**Large text**: 18pt (24px) or 14pt (18.67px) bold

---

### Mereka Color Contrast Ratios

#### Primary Colors on White

| Color | Hex | Ratio vs White | AA Normal | AA Large | AAA |
|-------|-----|----------------|-----------|----------|-----|
| **Teal** | `#2d898b` | 4.6:1 | Pass | Pass | Fail |
| **Magenta** | `#ab3b78` | 4.5:1 | Pass | Pass | Fail |
| **Blue** | `#295cad` | 4.8:1 | Pass | Pass | Fail |
| **Burgundy** | `#8c002f` | 8.2:1 | Pass | Pass | Pass |
| **Forest** | `#2c6e49` | 5.1:1 | Pass | Pass | Fail |
| **Orange** | `#e18437` | 2.8:1 | Fail | Pass | Fail |
| **Gold** | `#f4be48` | 1.8:1 | Fail | Fail | Fail |

#### White Text on Colors

| Background | Hex | Ratio vs White | AA Normal | AA Large |
|------------|-----|----------------|-----------|----------|
| **Teal** | `#2d898b` | 4.6:1 | Pass | Pass |
| **Magenta** | `#ab3b78` | 4.5:1 | Pass | Pass |
| **Blue** | `#295cad` | 4.8:1 | Pass | Pass |
| **Burgundy** | `#8c002f` | 8.2:1 | Pass | Pass |
| **Forest** | `#2c6e49` | 5.1:1 | Pass | Pass |
| **Gray-900** | `#1a1a1a` | 12.6:1 | Pass | Pass |

#### Text Colors

| Foreground | Background | Ratio | Usage |
|------------|------------|-------|-------|
| Gray-900 (`#1a1a1a`) | White | 12.6:1 | Primary text |
| Gray-600 (`#666666`) | White | 5.7:1 | Secondary text |
| Gray-500 (`#999999`) | White | 3.5:1 | Muted text (large only) |
| Gray-400 (`#cccccc`) | White | 1.9:1 | Decorative only |

---

### Safe Color Combinations

```tsx
// SAFE: High contrast combinations
<button className="bg-mereka-teal text-white">Book Now</button>
<button className="bg-mereka-burgundy text-white">Delete</button>
<p className="text-gray-900">Primary content</p>
<p className="text-gray-600">Secondary content</p>

// UNSAFE: Insufficient contrast - avoid these
<button className="bg-mereka-gold text-white">Low contrast</button>
<p className="text-gray-400">Too light for body text</p>
<button className="bg-mereka-orange text-white">Borderline</button>
```

### Using Low Contrast Colors Safely

Gold and Orange fail AA for normal text. Use them only for:

```tsx
// Decorative elements (no text)
<div className="bg-mereka-gold/20 p-4">
  <p className="text-gray-900">Content in high contrast</p>
</div>

// Icons with text labels
<span className="flex items-center gap-2">
  <StarIcon className="text-mereka-gold fill-mereka-gold" />
  <span className="text-gray-900">4.8 rating</span>
</span>

// Borders and accents
<div className="border-2 border-mereka-gold">
  <p className="text-gray-900">Highlighted content</p>
</div>
```

### Contrast Checking Component

```tsx
function ContrastChecker({
  foreground,
  background,
}: {
  foreground: string
  background: string
}) {
  const ratio = calculateContrastRatio(foreground, background)
  const passesAA = ratio >= 4.5
  const passesAAA = ratio >= 7

  return (
    <div
      style={{ backgroundColor: background, color: foreground }}
      className="p-4 rounded-lg"
    >
      <p className="font-medium">Sample Text</p>
      <p className="text-sm mt-2">
        Contrast: {ratio.toFixed(2)}:1
        {passesAAA ? ' (AAA)' : passesAA ? ' (AA)' : ' (Fail)'}
      </p>
    </div>
  )
}

// Utility function
function calculateContrastRatio(fg: string, bg: string): number {
  const getLuminance = (hex: string) => {
    const rgb = hexToRgb(hex)
    const [r, g, b] = rgb.map((c) => {
      c = c / 255
      return c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4)
    })
    return 0.2126 * r + 0.7152 * g + 0.0722 * b
  }

  const l1 = getLuminance(fg)
  const l2 = getLuminance(bg)
  const lighter = Math.max(l1, l2)
  const darker = Math.min(l1, l2)

  return (lighter + 0.05) / (darker + 0.05)
}
```

---

## 2. Keyboard Navigation

### Focus States

All interactive elements must have visible focus indicators.

```tsx
// Standard focus ring using Mereka teal
const focusStyles = 'focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2'

// Button focus
<button className={`
  bg-gray-900 text-white rounded-full px-4 py-2
  ${focusStyles}
`}>
  Submit
</button>

// Input focus
<input className={`
  border border-gray-300 rounded-lg px-4 py-2
  focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal
`} />

// Link focus
<a className={`
  text-mereka-blue underline
  ${focusStyles}
`}>
  Learn more
</a>
```

### Focus Ring Variants

```tsx
import { cva } from 'class-variance-authority'

const focusRingVariants = cva('focus:outline-none focus:ring-2 focus:ring-offset-2', {
  variants: {
    variant: {
      default: 'focus:ring-mereka-teal',
      error: 'focus:ring-red-500',
      inverse: 'focus:ring-white focus:ring-offset-gray-900',
    },
    offset: {
      none: 'focus:ring-offset-0',
      sm: 'focus:ring-offset-1',
      default: 'focus:ring-offset-2',
    },
  },
  defaultVariants: {
    variant: 'default',
    offset: 'default',
  },
})
```

### Tab Order

Ensure logical tab order follows visual layout.

```tsx
// Use tabIndex only when necessary
// 0 = follows natural order
// -1 = programmatically focusable only
// Positive values = anti-pattern, avoid

function NavigationMenu() {
  return (
    <nav aria-label="Main navigation">
      {/* Natural tab order follows DOM order */}
      <a href="/">Home</a>
      <a href="/experiences">Experiences</a>
      <a href="/about">About</a>

      {/* Search button - still in natural order */}
      <button aria-label="Search">
        <SearchIcon />
      </button>
    </nav>
  )
}

// Removing from tab order when hidden
function Dropdown({ isOpen, children }: { isOpen: boolean; children: React.ReactNode }) {
  return (
    <div
      role="menu"
      aria-hidden={!isOpen}
      // Hidden content should not be focusable
      tabIndex={isOpen ? 0 : -1}
    >
      {children}
    </div>
  )
}
```

### Skip Links

Provide skip links for keyboard users to bypass repetitive content.

```tsx
function SkipLinks() {
  return (
    <div className="sr-only focus-within:not-sr-only">
      <a
        href="#main-content"
        className="
          fixed top-4 left-4 z-[100]
          bg-gray-900 text-white px-4 py-2 rounded-lg
          focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2
          transform -translate-y-full focus:translate-y-0
          transition-transform duration-200
        "
      >
        Skip to main content
      </a>
      <a
        href="#footer-nav"
        className="
          fixed top-4 left-48 z-[100]
          bg-gray-900 text-white px-4 py-2 rounded-lg
          focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2
          transform -translate-y-full focus:translate-y-0
          transition-transform duration-200
        "
      >
        Skip to footer
      </a>
    </div>
  )
}

// Usage in layout
function Layout({ children }: { children: React.ReactNode }) {
  return (
    <>
      <SkipLinks />
      <Header />
      <main id="main-content" tabIndex={-1}>
        {children}
      </main>
      <Footer id="footer-nav" />
    </>
  )
}
```

### Keyboard Shortcuts

For custom keyboard shortcuts, always provide discoverability.

```tsx
function KeyboardShortcutsDialog() {
  return (
    <Dialog>
      <DialogContent>
        <DialogTitle>Keyboard Shortcuts</DialogTitle>
        <div className="space-y-4">
          <div className="flex justify-between">
            <span>Search</span>
            <kbd className="px-2 py-1 bg-gray-100 rounded text-sm font-mono">/</kbd>
          </div>
          <div className="flex justify-between">
            <span>Close dialog</span>
            <kbd className="px-2 py-1 bg-gray-100 rounded text-sm font-mono">Esc</kbd>
          </div>
          <div className="flex justify-between">
            <span>Navigate items</span>
            <div className="flex gap-1">
              <kbd className="px-2 py-1 bg-gray-100 rounded text-sm font-mono">Arrow Up</kbd>
              <kbd className="px-2 py-1 bg-gray-100 rounded text-sm font-mono">Arrow Down</kbd>
            </div>
          </div>
        </div>
      </DialogContent>
    </Dialog>
  )
}
```

---

## 3. Screen Readers

### ARIA Attributes

#### Landmark Roles

```tsx
function PageLayout() {
  return (
    <>
      {/* Header with navigation */}
      <header role="banner">
        <nav role="navigation" aria-label="Main">
          {/* Primary navigation */}
        </nav>
      </header>

      {/* Main content */}
      <main role="main" id="main-content">
        {/* Page content */}
      </main>

      {/* Sidebar */}
      <aside role="complementary" aria-label="Related experiences">
        {/* Sidebar content */}
      </aside>

      {/* Footer */}
      <footer role="contentinfo">
        <nav aria-label="Footer">
          {/* Footer navigation */}
        </nav>
      </footer>
    </>
  )
}
```

#### Button and Link ARIA

```tsx
// Icon-only buttons MUST have aria-label
<button aria-label="Close dialog">
  <XIcon className="w-5 h-5" />
</button>

<button aria-label="Add to favorites">
  <HeartIcon className="w-5 h-5" />
</button>

// Toggle buttons
<button
  aria-pressed={isPressed}
  aria-label="Toggle dark mode"
  onClick={() => setIsPressed(!isPressed)}
>
  {isPressed ? <MoonIcon /> : <SunIcon />}
</button>

// Expandable sections
<button
  aria-expanded={isExpanded}
  aria-controls="section-content"
  onClick={() => setIsExpanded(!isExpanded)}
>
  Show details
  <ChevronDownIcon className={isExpanded ? 'rotate-180' : ''} />
</button>
<div id="section-content" hidden={!isExpanded}>
  {/* Expandable content */}
</div>

// Links that open in new tab
<a
  href="https://external.com"
  target="_blank"
  rel="noopener noreferrer"
  aria-label="Visit external site (opens in new tab)"
>
  External Link
  <ExternalLinkIcon className="inline w-4 h-4 ml-1" aria-hidden="true" />
</a>
```

#### Dialog ARIA

```tsx
function AccessibleDialog({
  open,
  onClose,
  title,
  description,
  children,
}: {
  open: boolean
  onClose: () => void
  title: string
  description?: string
  children: React.ReactNode
}) {
  const titleId = useId()
  const descId = useId()

  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby={titleId}
      aria-describedby={description ? descId : undefined}
      hidden={!open}
    >
      <h2 id={titleId}>{title}</h2>
      {description && <p id={descId}>{description}</p>}
      {children}
    </div>
  )
}
```

### Live Regions

Use live regions to announce dynamic content changes.

```tsx
// Polite announcements (non-urgent)
function SearchResults({ count }: { count: number }) {
  return (
    <div aria-live="polite" aria-atomic="true">
      {count} results found
    </div>
  )
}

// Assertive announcements (urgent)
function ErrorAlert({ message }: { message: string }) {
  return (
    <div role="alert" aria-live="assertive">
      <AlertCircleIcon aria-hidden="true" />
      {message}
    </div>
  )
}

// Status messages
function SaveStatus({ status }: { status: 'saving' | 'saved' | 'error' }) {
  const messages = {
    saving: 'Saving changes...',
    saved: 'All changes saved',
    error: 'Failed to save changes',
  }

  return (
    <div role="status" aria-live="polite" className="sr-only">
      {messages[status]}
    </div>
  )
}

// Toast notifications
function Toast({ message, type }: { message: string; type: 'success' | 'error' | 'info' }) {
  return (
    <div
      role={type === 'error' ? 'alert' : 'status'}
      aria-live={type === 'error' ? 'assertive' : 'polite'}
      className="fixed bottom-4 right-4 p-4 rounded-lg shadow-lg"
    >
      {message}
    </div>
  )
}
```

### Screen Reader Only Content

```tsx
// Utility class for visually hidden but accessible content
// In Tailwind: "sr-only"
const srOnly = `
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
`

// Usage examples
function PriceDisplay({ price, currency }: { price: number; currency: string }) {
  return (
    <p>
      <span className="sr-only">Price:</span>
      <span aria-hidden="true">{currency}</span>
      <span className="sr-only">{currency === 'RM' ? 'Malaysian Ringgit' : currency}</span>
      {price.toLocaleString()}
    </p>
  )
}

function RatingStars({ rating }: { rating: number }) {
  return (
    <div>
      <span className="sr-only">{rating} out of 5 stars</span>
      <div aria-hidden="true">
        {[1, 2, 3, 4, 5].map((star) => (
          <StarIcon
            key={star}
            className={star <= rating ? 'text-mereka-gold fill-mereka-gold' : 'text-gray-300'}
          />
        ))}
      </div>
    </div>
  )
}

// Loading states
function LoadingButton({ isLoading, children }: { isLoading: boolean; children: React.ReactNode }) {
  return (
    <button disabled={isLoading}>
      {isLoading && (
        <>
          <LoadingSpinner aria-hidden="true" />
          <span className="sr-only">Loading, please wait</span>
        </>
      )}
      <span className={isLoading ? 'opacity-0' : ''}>{children}</span>
    </button>
  )
}
```

---

## 4. Focus Management

### Focus Trap in Modals

Trap focus within modals and dialogs while open.

```tsx
import { useRef, useEffect } from 'react'

function useFocusTrap(isActive: boolean) {
  const containerRef = useRef<HTMLDivElement>(null)
  const previousActiveElement = useRef<HTMLElement | null>(null)

  useEffect(() => {
    if (!isActive) return

    // Store the previously focused element
    previousActiveElement.current = document.activeElement as HTMLElement

    const container = containerRef.current
    if (!container) return

    // Get all focusable elements
    const focusableElements = container.querySelectorAll<HTMLElement>(
      'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
    )
    const firstElement = focusableElements[0]
    const lastElement = focusableElements[focusableElements.length - 1]

    // Focus the first element
    firstElement?.focus()

    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key !== 'Tab') return

      if (e.shiftKey) {
        // Shift + Tab: moving backwards
        if (document.activeElement === firstElement) {
          e.preventDefault()
          lastElement?.focus()
        }
      } else {
        // Tab: moving forwards
        if (document.activeElement === lastElement) {
          e.preventDefault()
          firstElement?.focus()
        }
      }
    }

    container.addEventListener('keydown', handleKeyDown)

    return () => {
      container.removeEventListener('keydown', handleKeyDown)
      // Restore focus when modal closes
      previousActiveElement.current?.focus()
    }
  }, [isActive])

  return containerRef
}

// Usage
function Modal({ open, onClose, children }: ModalProps) {
  const containerRef = useFocusTrap(open)

  if (!open) return null

  return (
    <div ref={containerRef} role="dialog" aria-modal="true">
      {children}
    </div>
  )
}
```

### Focus Restoration

Always restore focus when closing modals or navigating.

```tsx
function useRestoreFocus() {
  const triggerRef = useRef<HTMLElement | null>(null)

  const saveFocus = () => {
    triggerRef.current = document.activeElement as HTMLElement
  }

  const restoreFocus = () => {
    triggerRef.current?.focus()
    triggerRef.current = null
  }

  return { saveFocus, restoreFocus }
}

// Complete modal with focus management
function AccessibleModal({
  open,
  onClose,
  children,
}: {
  open: boolean
  onClose: () => void
  children: React.ReactNode
}) {
  const { saveFocus, restoreFocus } = useRestoreFocus()
  const modalRef = useFocusTrap(open)

  useEffect(() => {
    if (open) {
      saveFocus()
    } else {
      restoreFocus()
    }
  }, [open])

  // Handle Escape key
  useEffect(() => {
    const handleEscape = (e: KeyboardEvent) => {
      if (e.key === 'Escape' && open) {
        onClose()
      }
    }
    document.addEventListener('keydown', handleEscape)
    return () => document.removeEventListener('keydown', handleEscape)
  }, [open, onClose])

  if (!open) return null

  return (
    <div className="fixed inset-0 z-50">
      {/* Backdrop */}
      <div
        className="fixed inset-0 bg-black/50"
        onClick={onClose}
        aria-hidden="true"
      />

      {/* Modal */}
      <div
        ref={modalRef}
        role="dialog"
        aria-modal="true"
        className="fixed left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 bg-white rounded-xl"
      >
        {children}
      </div>
    </div>
  )
}
```

### Focus After Actions

Move focus appropriately after user actions.

```tsx
function DeleteItemList() {
  const [items, setItems] = useState(['Item 1', 'Item 2', 'Item 3'])
  const itemRefs = useRef<Map<number, HTMLButtonElement>>(new Map())

  const handleDelete = (index: number) => {
    setItems((prev) => prev.filter((_, i) => i !== index))

    // Focus the next item, or previous if deleting last
    requestAnimationFrame(() => {
      const nextIndex = index < items.length - 1 ? index : index - 1
      if (nextIndex >= 0) {
        itemRefs.current.get(nextIndex)?.focus()
      }
    })
  }

  return (
    <ul role="list">
      {items.map((item, index) => (
        <li key={item} className="flex items-center justify-between">
          <span>{item}</span>
          <button
            ref={(el) => el && itemRefs.current.set(index, el)}
            onClick={() => handleDelete(index)}
            aria-label={`Delete ${item}`}
          >
            <TrashIcon />
          </button>
        </li>
      ))}
    </ul>
  )
}
```

---

## 5. Form Accessibility

### Labels

Every form input must have an associated label.

```tsx
// Explicit label association (preferred)
function LabeledInput({ id, label, ...props }: InputProps) {
  const inputId = id || useId()

  return (
    <div>
      <label htmlFor={inputId} className="block text-sm font-medium text-gray-700">
        {label}
      </label>
      <input id={inputId} {...props} />
    </div>
  )
}

// Implicit label (wrapping)
function WrappedInput({ label, ...props }: InputProps) {
  return (
    <label className="block">
      <span className="text-sm font-medium text-gray-700">{label}</span>
      <input {...props} className="mt-1 block w-full" />
    </label>
  )
}

// aria-label for inputs without visible labels
<input
  type="search"
  aria-label="Search experiences"
  placeholder="Search..."
/>

// aria-labelledby for complex labels
<div>
  <span id="price-label">Price</span>
  <span id="price-currency">(RM)</span>
  <input
    type="number"
    aria-labelledby="price-label price-currency"
  />
</div>
```

### Required Fields

Clearly indicate required fields.

```tsx
function RequiredInput({
  label,
  required,
  id,
  ...props
}: {
  label: string
  required?: boolean
  id?: string
}) {
  const inputId = id || useId()

  return (
    <div>
      <label htmlFor={inputId} className="block text-sm font-medium text-gray-700">
        {label}
        {required && (
          <>
            <span className="text-red-500 ml-1" aria-hidden="true">*</span>
            <span className="sr-only">(required)</span>
          </>
        )}
      </label>
      <input
        id={inputId}
        required={required}
        aria-required={required}
        {...props}
      />
    </div>
  )
}

// Form with required field legend
function BookingForm() {
  return (
    <form>
      <p className="text-sm text-gray-500 mb-4">
        <span className="text-red-500" aria-hidden="true">*</span>
        <span className="sr-only">Asterisk</span> indicates required field
      </p>

      <RequiredInput label="Full Name" required />
      <RequiredInput label="Email" type="email" required />
      <Input label="Phone (optional)" type="tel" />
    </form>
  )
}
```

### Error Announcements

Announce validation errors to screen readers.

```tsx
interface FormFieldProps {
  label: string
  error?: string
  description?: string
  required?: boolean
  children: React.ReactNode
}

function FormField({
  label,
  error,
  description,
  required,
  children,
}: FormFieldProps) {
  const id = useId()
  const errorId = `${id}-error`
  const descId = `${id}-description`

  return (
    <div className="space-y-1.5">
      <label htmlFor={id} className="block text-sm font-medium text-gray-700">
        {label}
        {required && <span className="text-red-500 ml-1">*</span>}
      </label>

      {description && (
        <p id={descId} className="text-sm text-gray-500">
          {description}
        </p>
      )}

      {/* Clone child to add accessibility attributes */}
      {React.cloneElement(children as React.ReactElement, {
        id,
        'aria-invalid': !!error,
        'aria-describedby': [
          description ? descId : null,
          error ? errorId : null,
        ].filter(Boolean).join(' ') || undefined,
        'aria-required': required,
      })}

      {error && (
        <p
          id={errorId}
          role="alert"
          className="flex items-center gap-1 text-sm text-red-600"
        >
          <AlertCircleIcon className="w-4 h-4" aria-hidden="true" />
          {error}
        </p>
      )}
    </div>
  )
}

// Usage
<FormField
  label="Email Address"
  required
  error={errors.email?.message}
  description="We'll never share your email"
>
  <input type="email" className="..." />
</FormField>
```

### Form Submission Feedback

```tsx
function AccessibleForm() {
  const [status, setStatus] = useState<'idle' | 'submitting' | 'success' | 'error'>('idle')
  const [errorMessage, setErrorMessage] = useState('')
  const formRef = useRef<HTMLFormElement>(null)

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    setStatus('submitting')

    try {
      await submitForm()
      setStatus('success')
    } catch (err) {
      setStatus('error')
      setErrorMessage(err.message)
      // Focus the error message for screen readers
      formRef.current?.querySelector('[role="alert"]')?.focus()
    }
  }

  return (
    <form ref={formRef} onSubmit={handleSubmit}>
      {/* Form-level error announcement */}
      {status === 'error' && (
        <div
          role="alert"
          tabIndex={-1}
          className="p-4 mb-4 bg-red-50 border border-red-200 rounded-lg"
        >
          <h3 className="font-medium text-red-800">Submission Error</h3>
          <p className="text-red-700">{errorMessage}</p>
        </div>
      )}

      {/* Success announcement */}
      {status === 'success' && (
        <div
          role="status"
          aria-live="polite"
          className="p-4 mb-4 bg-green-50 border border-green-200 rounded-lg"
        >
          <p className="text-green-800">Form submitted successfully!</p>
        </div>
      )}

      {/* Form fields */}
      <FormField label="Name" required>
        <input type="text" />
      </FormField>

      <button type="submit" disabled={status === 'submitting'}>
        {status === 'submitting' ? (
          <>
            <LoadingSpinner aria-hidden="true" />
            <span className="sr-only">Submitting form, please wait</span>
            <span aria-hidden="true">Submitting...</span>
          </>
        ) : (
          'Submit'
        )}
      </button>
    </form>
  )
}
```

### Fieldset and Legend

Group related form controls.

```tsx
function PaymentMethodSelector() {
  return (
    <fieldset className="border border-gray-200 rounded-lg p-4">
      <legend className="text-sm font-medium text-gray-900 px-2">
        Payment Method
      </legend>

      <div className="space-y-3 mt-2">
        <label className="flex items-center gap-3">
          <input
            type="radio"
            name="payment"
            value="card"
            className="w-4 h-4 text-mereka-teal focus:ring-mereka-teal"
          />
          <span>Credit Card</span>
        </label>

        <label className="flex items-center gap-3">
          <input
            type="radio"
            name="payment"
            value="bank"
            className="w-4 h-4 text-mereka-teal focus:ring-mereka-teal"
          />
          <span>Bank Transfer</span>
        </label>

        <label className="flex items-center gap-3">
          <input
            type="radio"
            name="payment"
            value="wallet"
            className="w-4 h-4 text-mereka-teal focus:ring-mereka-teal"
          />
          <span>E-Wallet</span>
        </label>
      </div>
    </fieldset>
  )
}
```

---

## 6. Image Accessibility

### Alt Text Guidelines

| Image Type | Alt Text Approach | Example |
|------------|-------------------|---------|
| **Informative** | Describe the content and meaning | "Chef preparing sushi in traditional kitchen" |
| **Decorative** | Empty alt or `aria-hidden` | `alt=""` or `aria-hidden="true"` |
| **Functional** | Describe the action | "Search" for search icon button |
| **Complex** | Brief alt + detailed description | Use `aria-describedby` |

```tsx
// Informative image
<img
  src="/experience-pottery.jpg"
  alt="Hands shaping clay on a pottery wheel, with finished ceramic bowls in the background"
/>

// Decorative image
<img
  src="/decorative-pattern.svg"
  alt=""
  aria-hidden="true"
/>

// Background image (decorative)
<div
  className="bg-cover bg-center"
  style={{ backgroundImage: 'url(/hero-bg.jpg)' }}
  role="presentation"
/>

// Functional image (in button)
<button aria-label="Add to favorites">
  <img src="/heart-icon.svg" alt="" aria-hidden="true" />
</button>

// Complex image with extended description
<figure>
  <img
    src="/experience-map.png"
    alt="Map showing experience location in George Town, Penang"
    aria-describedby="map-description"
  />
  <figcaption id="map-description">
    The pottery studio is located at 123 Armenian Street, Georgetown, Penang.
    Nearest landmark is the Cheong Fatt Tze Blue Mansion, a 5-minute walk away.
    Public parking available at KOMTAR.
  </figcaption>
</figure>
```

### Image Component with Accessibility

```tsx
interface AccessibleImageProps {
  src: string
  alt: string
  isDecorative?: boolean
  caption?: string
  className?: string
}

function AccessibleImage({
  src,
  alt,
  isDecorative = false,
  caption,
  className,
}: AccessibleImageProps) {
  const captionId = useId()

  if (isDecorative) {
    return (
      <img
        src={src}
        alt=""
        aria-hidden="true"
        className={className}
      />
    )
  }

  if (caption) {
    return (
      <figure>
        <img
          src={src}
          alt={alt}
          aria-describedby={captionId}
          className={className}
        />
        <figcaption id={captionId} className="text-sm text-gray-600 mt-2">
          {caption}
        </figcaption>
      </figure>
    )
  }

  return <img src={src} alt={alt} className={className} />
}
```

### Avatar Images

```tsx
function Avatar({
  src,
  name,
  size = 'md',
}: {
  src?: string
  name: string
  size?: 'sm' | 'md' | 'lg'
}) {
  const sizes = {
    sm: 'w-8 h-8',
    md: 'w-10 h-10',
    lg: 'w-16 h-16',
  }

  if (!src) {
    // Initials fallback
    const initials = name
      .split(' ')
      .map((n) => n[0])
      .join('')
      .toUpperCase()
      .slice(0, 2)

    return (
      <div
        className={`${sizes[size]} rounded-full bg-mereka-teal flex items-center justify-center`}
        role="img"
        aria-label={name}
      >
        <span className="text-white font-medium" aria-hidden="true">
          {initials}
        </span>
      </div>
    )
  }

  return (
    <img
      src={src}
      alt={`${name}'s profile photo`}
      className={`${sizes[size]} rounded-full object-cover`}
    />
  )
}
```

---

## 7. Motion and Animation

### Reduced Motion Support

Respect user preferences for reduced motion.

```tsx
// CSS approach with Tailwind
// In your global CSS or tailwind.config.ts:

// tailwind.config.ts
module.exports = {
  theme: {
    extend: {
      // Define motion-safe animations
      animation: {
        'fade-in': 'fadeIn 200ms ease-out',
        'slide-up': 'slideUp 300ms ease-out',
        'spin-slow': 'spin 3s linear infinite',
      },
    },
  },
}

// CSS
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
```

### React Hook for Motion Preference

```tsx
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
    <div
      className={`
        transform transition-all duration-300
        ${prefersReducedMotion
          ? ''
          : 'hover:scale-105 hover:shadow-lg'
        }
      `}
    >
      {children}
    </div>
  )
}
```

### Safe Animation Patterns

```tsx
// Fade animations (generally safe)
const fadeIn = 'animate-fade-in motion-reduce:animate-none'

// Slide animations (provide alternative)
function SlideInPanel({ isOpen, children }: { isOpen: boolean; children: React.ReactNode }) {
  return (
    <div
      className={`
        transform transition-transform duration-300
        motion-reduce:transition-none
        ${isOpen
          ? 'translate-x-0'
          : '-translate-x-full motion-reduce:hidden'
        }
      `}
    >
      {children}
    </div>
  )
}

// Loading spinners (essential motion)
function LoadingSpinner() {
  return (
    <div className="relative">
      {/* Visual spinner for motion-tolerant users */}
      <div
        className="w-6 h-6 border-2 border-mereka-teal border-t-transparent rounded-full animate-spin motion-reduce:hidden"
        aria-hidden="true"
      />
      {/* Static alternative for reduced motion */}
      <div
        className="hidden motion-reduce:block w-6 h-6 border-2 border-mereka-teal rounded-full animate-pulse"
        aria-hidden="true"
      />
      <span className="sr-only">Loading</span>
    </div>
  )
}

// Auto-playing content
function Carousel({ slides }: { slides: Slide[] }) {
  const prefersReducedMotion = usePrefersReducedMotion()
  const [isPaused, setIsPaused] = useState(prefersReducedMotion)

  useEffect(() => {
    if (prefersReducedMotion) {
      setIsPaused(true)
    }
  }, [prefersReducedMotion])

  return (
    <div>
      <div className="relative">
        {/* Carousel content */}
      </div>
      {/* Always provide pause control */}
      <button
        onClick={() => setIsPaused(!isPaused)}
        aria-label={isPaused ? 'Play slideshow' : 'Pause slideshow'}
      >
        {isPaused ? <PlayIcon /> : <PauseIcon />}
      </button>
    </div>
  )
}
```

---

## 8. Touch Targets

### Minimum Size Requirements

| Standard | Minimum Size | Recommended |
|----------|--------------|-------------|
| **WCAG 2.1 AAA** | 44x44px | - |
| **Apple HIG** | 44x44pt | 48x48pt |
| **Material Design** | 48x48dp | - |
| **Mereka Standard** | 44x44px | 48x48px |

```tsx
// Touch-friendly button
<button className="
  min-h-[44px] min-w-[44px]
  px-4 py-2
  touch-manipulation
">
  Tap me
</button>

// Icon button with adequate touch target
<button className="
  p-3  /* 12px padding around 20px icon = 44px total */
  touch-manipulation
">
  <MenuIcon className="w-5 h-5" />
</button>

// Link with touch padding
<a className="
  inline-flex items-center
  min-h-[44px]
  py-2 px-1 -mx-1  /* Extend tap area without affecting layout */
">
  Learn more
</a>
```

### Touch Target Spacing

```tsx
// Adequate spacing between touch targets
function ActionBar() {
  return (
    <div className="flex items-center gap-2">
      {/* Each button is 44px+ with 8px gap */}
      <button className="p-3" aria-label="Like">
        <HeartIcon className="w-5 h-5" />
      </button>
      <button className="p-3" aria-label="Comment">
        <CommentIcon className="w-5 h-5" />
      </button>
      <button className="p-3" aria-label="Share">
        <ShareIcon className="w-5 h-5" />
      </button>
    </div>
  )
}

// List items with touch-friendly height
function TouchFriendlyList({ items }: { items: Item[] }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.id}>
          <a
            href={item.href}
            className="
              flex items-center
              min-h-[48px]
              px-4 py-3
              border-b border-gray-100
              active:bg-gray-50
            "
          >
            {item.label}
          </a>
        </li>
      ))}
    </ul>
  )
}
```

### Touch Target Component

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const touchTargetVariants = cva(
  'touch-manipulation inline-flex items-center justify-center',
  {
    variants: {
      size: {
        default: 'min-h-[44px] min-w-[44px]',
        lg: 'min-h-[48px] min-w-[48px]',
        xl: 'min-h-[56px] min-w-[56px]',
      },
    },
    defaultVariants: {
      size: 'default',
    },
  }
)

interface TouchTargetProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof touchTargetVariants> {}

function TouchTarget({ size, className, ...props }: TouchTargetProps) {
  return (
    <button
      className={touchTargetVariants({ size, className })}
      {...props}
    />
  )
}

// Extend existing buttons
function AccessibleIconButton({
  icon,
  label,
  ...props
}: {
  icon: React.ReactNode
  label: string
}) {
  return (
    <TouchTarget aria-label={label} {...props}>
      <span className="w-5 h-5" aria-hidden="true">
        {icon}
      </span>
    </TouchTarget>
  )
}
```

### Mobile Form Inputs

```tsx
// Touch-friendly form inputs
function MobileInput({ label, ...props }: InputProps) {
  return (
    <div className="space-y-1.5">
      <label className="block text-sm font-medium text-gray-700">
        {label}
      </label>
      <input
        className="
          w-full
          h-12  /* 48px for easy touch */
          px-4
          text-base  /* 16px to prevent iOS zoom */
          rounded-lg
          border border-gray-300
          focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal
        "
        {...props}
      />
    </div>
  )
}

// Touch-friendly checkbox
function TouchCheckbox({ label, ...props }: CheckboxProps) {
  return (
    <label className="
      flex items-center gap-3
      min-h-[44px]
      cursor-pointer
    ">
      <input
        type="checkbox"
        className="
          w-5 h-5
          rounded
          border-gray-300
          text-mereka-teal
          focus:ring-mereka-teal
        "
        {...props}
      />
      <span className="text-gray-900">{label}</span>
    </label>
  )
}
```

---

## 9. Testing Tools

### Browser Extensions

| Tool | Browser | Purpose |
|------|---------|---------|
| **axe DevTools** | Chrome, Firefox, Edge | Automated accessibility testing |
| **WAVE** | Chrome, Firefox | Visual accessibility evaluation |
| **Lighthouse** | Chrome (built-in) | Performance and accessibility audits |
| **Accessibility Insights** | Chrome, Edge | Detailed accessibility testing |
| **Color Contrast Analyzer** | Chrome | Color contrast checking |
| **HeadingsMap** | Chrome, Firefox | Document structure visualization |
| **Screen Reader Simulator** | Chrome | Experience screen reader behavior |

### Recommended Testing Workflow

```bash
# 1. Automated testing during development
npm install -D @axe-core/react

# In your app
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App'

if (process.env.NODE_ENV !== 'production') {
  const axe = require('@axe-core/react')
  axe(React, ReactDOM, 1000)
}

ReactDOM.render(<App />, document.getElementById('root'))
```

### Jest + Testing Library Setup

```tsx
// jest.setup.ts
import '@testing-library/jest-dom'
import { toHaveNoViolations } from 'jest-axe'

expect.extend(toHaveNoViolations)

// Example accessibility test
import { render } from '@testing-library/react'
import { axe } from 'jest-axe'

describe('Button', () => {
  it('should have no accessibility violations', async () => {
    const { container } = render(
      <button>Click me</button>
    )
    const results = await axe(container)
    expect(results).toHaveNoViolations()
  })
})

describe('Form', () => {
  it('should have proper label associations', () => {
    render(
      <FormField label="Email" required>
        <input type="email" />
      </FormField>
    )

    const input = screen.getByLabelText(/email/i)
    expect(input).toBeInTheDocument()
    expect(input).toHaveAttribute('aria-required', 'true')
  })

  it('should announce errors to screen readers', () => {
    render(
      <FormField label="Email" error="Invalid email">
        <input type="email" />
      </FormField>
    )

    const error = screen.getByRole('alert')
    expect(error).toHaveTextContent('Invalid email')
  })
})
```

### Playwright Accessibility Testing

```typescript
// e2e/accessibility.spec.ts
import { test, expect } from '@playwright/test'
import AxeBuilder from '@axe-core/playwright'

test.describe('Accessibility', () => {
  test('homepage should have no violations', async ({ page }) => {
    await page.goto('/')

    const results = await new AxeBuilder({ page })
      .withTags(['wcag2a', 'wcag2aa', 'wcag21aa'])
      .analyze()

    expect(results.violations).toEqual([])
  })

  test('booking form should be keyboard accessible', async ({ page }) => {
    await page.goto('/book/experience-123')

    // Tab through form
    await page.keyboard.press('Tab')
    await expect(page.locator('input[name="name"]')).toBeFocused()

    await page.keyboard.press('Tab')
    await expect(page.locator('input[name="email"]')).toBeFocused()

    await page.keyboard.press('Tab')
    await expect(page.locator('input[name="phone"]')).toBeFocused()

    // Submit with Enter
    await page.keyboard.press('Tab')
    await page.keyboard.press('Enter')
  })

  test('modal should trap focus', async ({ page }) => {
    await page.goto('/experiences')

    // Open modal
    await page.click('button:has-text("Book Now")')

    // Verify focus is inside modal
    await expect(page.locator('[role="dialog"]')).toBeFocused()

    // Tab should cycle within modal
    const modalButtons = page.locator('[role="dialog"] button')
    const buttonCount = await modalButtons.count()

    for (let i = 0; i < buttonCount + 1; i++) {
      await page.keyboard.press('Tab')
    }

    // Focus should still be in modal
    const focusedElement = page.locator(':focus')
    await expect(focusedElement).toBeVisible()

    const isInModal = await page.evaluate(() => {
      const focused = document.activeElement
      const modal = document.querySelector('[role="dialog"]')
      return modal?.contains(focused)
    })
    expect(isInModal).toBe(true)
  })
})
```

### Manual Testing Checklist

```markdown
## Accessibility Testing Checklist

### Keyboard Navigation
- [ ] Can reach all interactive elements with Tab
- [ ] Tab order follows logical reading order
- [ ] Focus is visible on all focused elements
- [ ] Escape closes modals and dropdowns
- [ ] Arrow keys work in menus and lists
- [ ] Enter/Space activates buttons and links

### Screen Reader
- [ ] Page has proper heading hierarchy (h1 > h2 > h3)
- [ ] Images have appropriate alt text
- [ ] Form inputs have associated labels
- [ ] Error messages are announced
- [ ] Dynamic content updates are announced
- [ ] Landmarks are properly defined

### Visual
- [ ] Color contrast meets AA requirements
- [ ] Information is not conveyed by color alone
- [ ] Text can be resized to 200% without loss
- [ ] Focus indicators are visible
- [ ] Animations respect reduced motion preference

### Forms
- [ ] All inputs have visible labels
- [ ] Required fields are marked
- [ ] Error messages are clear and helpful
- [ ] Form can be submitted with keyboard

### Mobile
- [ ] Touch targets are at least 44x44px
- [ ] Adequate spacing between targets
- [ ] Zoom is not disabled
- [ ] Content is readable without horizontal scroll
```

---

## Quick Reference

### Essential ARIA

| Attribute | Purpose | Example |
|-----------|---------|---------|
| `aria-label` | Accessible name for element | `aria-label="Close dialog"` |
| `aria-labelledby` | Reference to labeling element | `aria-labelledby="title-id"` |
| `aria-describedby` | Reference to describing element | `aria-describedby="error-id"` |
| `aria-hidden` | Hide from screen readers | `aria-hidden="true"` |
| `aria-expanded` | Expansion state | `aria-expanded="false"` |
| `aria-pressed` | Toggle state | `aria-pressed="true"` |
| `aria-live` | Live region | `aria-live="polite"` |
| `aria-invalid` | Validation state | `aria-invalid="true"` |
| `aria-required` | Required field | `aria-required="true"` |
| `aria-modal` | Modal dialog | `aria-modal="true"` |

### Common Patterns

```tsx
// Accessible button
<button
  type="button"
  aria-label="Close"
  onClick={onClose}
  className="focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2"
>
  <XIcon aria-hidden="true" />
</button>

// Accessible link
<a
  href="/page"
  className="focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2"
>
  Visible text
</a>

// Accessible input
<input
  id="email"
  type="email"
  aria-label="Email address"
  aria-required="true"
  aria-invalid={hasError}
  aria-describedby={hasError ? 'email-error' : undefined}
/>

// Accessible modal
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="modal-title"
  aria-describedby="modal-description"
>
  <h2 id="modal-title">Title</h2>
  <p id="modal-description">Description</p>
</div>
```

---

## Related Documentation

- [Colors](./colors.md) - Color palette and contrast ratios
- [Buttons](./buttons.md) - Button focus states and keyboard interaction
- [Inputs](./inputs.md) - Form input accessibility
- [Dialogs](./dialogs.md) - Modal accessibility patterns
- [Forms](./forms.md) - Complete form accessibility
- [Navigation](./navigation.md) - Navigation keyboard patterns
