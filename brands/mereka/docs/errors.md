# Error States

> Comprehensive error handling patterns for the Mereka platform, covering form validation, API errors, HTTP error pages, empty states, and recovery patterns.

**Figma Sections**: Feedback, Dialogs, Pages

---

## Error Architecture

```
Error States
├── Form Validation         ← Inline field errors, error summaries
├── API/Network Errors      ← Failed requests, timeouts, offline
├── HTTP Error Pages        ← 404, 500, 403
├── Empty States            ← No results, no data
├── Payment Errors          ← Failed transactions
├── Session Errors          ← Expired, unauthorized
├── Error Boundaries        ← React component errors
└── Retry Patterns          ← Recovery mechanisms
```

---

## Error Color System

| Semantic | Token | Hex | Usage |
|----------|-------|-----|-------|
| **Error** | `mereka-burgundy` | `#8c002f` | Error text, borders, icons |
| **Error Light** | `mereka-pink` | `#cd89ae` | Error backgrounds |
| **Error Background** | `red-50` | `#fef2f2` | Light error surfaces |

### Tailwind Classes

```css
/* Error text */
.text-mereka-burgundy { color: #8c002f; }

/* Error border */
.border-mereka-burgundy { border-color: #8c002f; }

/* Error background */
.bg-red-50 { background-color: #fef2f2; }
.bg-mereka-pink/10 { background-color: rgba(205, 137, 174, 0.1); }
```

---

## 1. Form Validation Errors

### Inline Field Errors

Display errors directly below the input field.

```tsx
import { AlertCircle } from 'lucide-react'

interface FormFieldProps {
  label: string
  error?: string
  children: React.ReactNode
  required?: boolean
}

export function FormField({
  label,
  error,
  children,
  required,
}: FormFieldProps) {
  return (
    <div className="space-y-1.5">
      <label className="block font-body text-body-sm font-medium text-gray-700">
        {label}
        {required && <span className="text-mereka-burgundy ml-1">*</span>}
      </label>
      {children}
      {error && (
        <p
          className="flex items-center gap-1.5 text-caption text-mereka-burgundy"
          role="alert"
        >
          <AlertCircle className="w-3.5 h-3.5 flex-shrink-0" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Input with Error State

```tsx
import { cva, type VariantProps } from 'class-variance-authority'
import { AlertCircle } from 'lucide-react'

const inputVariants = cva(
  'w-full h-11 px-4 rounded-lg border font-body text-body transition-all duration-150 placeholder:text-gray-400 focus:outline-none focus:ring-2',
  {
    variants: {
      state: {
        default: 'border-gray-300 bg-white text-gray-900 focus:ring-mereka-teal/20 focus:border-mereka-teal',
        error: 'border-mereka-burgundy bg-red-50/50 text-gray-900 focus:ring-mereka-burgundy/20 focus:border-mereka-burgundy',
        disabled: 'border-gray-200 bg-gray-100 text-gray-400 cursor-not-allowed',
      },
    },
    defaultVariants: {
      state: 'default',
    },
  }
)

interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label?: string
  error?: string
}

export function Input({ label, error, disabled, className, id, ...props }: InputProps) {
  const inputId = id || `input-${Math.random().toString(36).substr(2, 9)}`
  const errorId = `${inputId}-error`
  const state = disabled ? 'disabled' : error ? 'error' : 'default'

  return (
    <div className="space-y-1.5">
      {label && (
        <label
          htmlFor={inputId}
          className="block font-body text-body-sm font-medium text-gray-700"
        >
          {label}
        </label>
      )}
      <div className="relative">
        <input
          id={inputId}
          disabled={disabled}
          aria-invalid={!!error}
          aria-describedby={error ? errorId : undefined}
          className={inputVariants({ state, className })}
          {...props}
        />
        {error && (
          <AlertCircle className="absolute right-3 top-1/2 -translate-y-1/2 w-5 h-5 text-mereka-burgundy" />
        )}
      </div>
      {error && (
        <p
          id={errorId}
          className="flex items-center gap-1.5 text-caption text-mereka-burgundy"
          role="alert"
        >
          <AlertCircle className="w-3.5 h-3.5 flex-shrink-0" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Error Summary (Form-Level)

Display all form errors in a summary box at the top or bottom of the form.

```tsx
import { AlertCircle, X } from 'lucide-react'

interface FormError {
  field: string
  message: string
}

interface ErrorSummaryProps {
  title?: string
  errors: FormError[]
  onDismiss?: () => void
  onFieldClick?: (field: string) => void
}

export function ErrorSummary({
  title = 'Please fix the following errors:',
  errors,
  onDismiss,
  onFieldClick,
}: ErrorSummaryProps) {
  if (errors.length === 0) return null

  return (
    <div
      className="bg-red-50 border border-mereka-burgundy/20 rounded-xl p-4"
      role="alert"
      aria-live="polite"
    >
      <div className="flex items-start gap-3">
        <div className="flex-shrink-0 w-10 h-10 bg-mereka-burgundy/10 rounded-full flex items-center justify-center">
          <AlertCircle className="w-5 h-5 text-mereka-burgundy" />
        </div>

        <div className="flex-1 min-w-0">
          <h3 className="font-heading text-h5 text-mereka-burgundy">{title}</h3>
          <ul className="mt-2 space-y-1">
            {errors.map((error, index) => (
              <li key={index} className="text-body-sm text-mereka-burgundy">
                {onFieldClick ? (
                  <button
                    type="button"
                    onClick={() => onFieldClick(error.field)}
                    className="underline hover:no-underline text-left"
                  >
                    {error.message}
                  </button>
                ) : (
                  error.message
                )}
              </li>
            ))}
          </ul>
        </div>

        {onDismiss && (
          <button
            type="button"
            onClick={onDismiss}
            className="flex-shrink-0 p-1 rounded-full hover:bg-mereka-burgundy/10 transition-colors"
            aria-label="Dismiss errors"
          >
            <X className="w-5 h-5 text-mereka-burgundy" />
          </button>
        )}
      </div>
    </div>
  )
}
```

### Usage Example

```tsx
function BookingForm() {
  const [errors, setErrors] = useState<FormError[]>([])

  const focusField = (field: string) => {
    const element = document.getElementById(field)
    element?.focus()
    element?.scrollIntoView({ behavior: 'smooth', block: 'center' })
  }

  return (
    <form className="space-y-6">
      <ErrorSummary
        errors={errors}
        onFieldClick={focusField}
        onDismiss={() => setErrors([])}
      />

      <Input
        id="email"
        label="Email"
        type="email"
        error={errors.find((e) => e.field === 'email')?.message}
      />

      <Input
        id="phone"
        label="Phone Number"
        type="tel"
        error={errors.find((e) => e.field === 'phone')?.message}
      />

      <Button type="submit" variant="solid">
        Continue
      </Button>
    </form>
  )
}
```

---

## 2. API/Network Errors

### Error Alert Component

```tsx
import { AlertTriangle, RefreshCw, Wifi, WifiOff, X } from 'lucide-react'
import { cva, type VariantProps } from 'class-variance-authority'

const alertVariants = cva(
  'rounded-xl p-4 flex items-start gap-3',
  {
    variants: {
      variant: {
        error: 'bg-red-50 border border-mereka-burgundy/20',
        warning: 'bg-yellow-50 border border-yellow-200',
        offline: 'bg-gray-100 border border-gray-300',
      },
    },
    defaultVariants: {
      variant: 'error',
    },
  }
)

const iconVariants = {
  error: { icon: AlertTriangle, className: 'text-mereka-burgundy' },
  warning: { icon: AlertTriangle, className: 'text-mereka-orange' },
  offline: { icon: WifiOff, className: 'text-gray-600' },
}

interface ApiErrorAlertProps extends VariantProps<typeof alertVariants> {
  title: string
  message: string
  onRetry?: () => void
  onDismiss?: () => void
  retrying?: boolean
}

export function ApiErrorAlert({
  variant = 'error',
  title,
  message,
  onRetry,
  onDismiss,
  retrying,
}: ApiErrorAlertProps) {
  const { icon: Icon, className: iconClass } = iconVariants[variant!]

  return (
    <div className={alertVariants({ variant })} role="alert">
      <div className="flex-shrink-0 w-10 h-10 bg-white rounded-full flex items-center justify-center shadow-sm">
        <Icon className={`w-5 h-5 ${iconClass}`} />
      </div>

      <div className="flex-1 min-w-0">
        <h3 className="font-heading text-h5 text-gray-900">{title}</h3>
        <p className="text-body-sm text-gray-600 mt-1">{message}</p>

        {onRetry && (
          <button
            type="button"
            onClick={onRetry}
            disabled={retrying}
            className="inline-flex items-center gap-2 mt-3 text-body-sm font-medium text-mereka-teal hover:underline disabled:opacity-50 disabled:cursor-not-allowed"
          >
            <RefreshCw className={`w-4 h-4 ${retrying ? 'animate-spin' : ''}`} />
            {retrying ? 'Retrying...' : 'Try again'}
          </button>
        )}
      </div>

      {onDismiss && (
        <button
          type="button"
          onClick={onDismiss}
          className="flex-shrink-0 p-1 rounded-full hover:bg-black/5 transition-colors"
          aria-label="Dismiss"
        >
          <X className="w-5 h-5 text-gray-400" />
        </button>
      )}
    </div>
  )
}
```

### Network Error States

```tsx
// Request failed
<ApiErrorAlert
  variant="error"
  title="Request failed"
  message="We couldn't complete your request. Please check your connection and try again."
  onRetry={handleRetry}
/>

// Timeout
<ApiErrorAlert
  variant="error"
  title="Request timed out"
  message="The server took too long to respond. This might be a temporary issue."
  onRetry={handleRetry}
/>

// Offline
<ApiErrorAlert
  variant="offline"
  title="You're offline"
  message="Check your internet connection and try again."
  onRetry={handleRetry}
/>

// Rate limited
<ApiErrorAlert
  variant="warning"
  title="Too many requests"
  message="Please wait a moment before trying again."
/>
```

### Offline Banner

Persistent banner when user loses connection.

```tsx
import { WifiOff, RefreshCw } from 'lucide-react'
import { useState, useEffect } from 'react'

export function OfflineBanner() {
  const [isOffline, setIsOffline] = useState(!navigator.onLine)
  const [isRetrying, setIsRetrying] = useState(false)

  useEffect(() => {
    const handleOnline = () => setIsOffline(false)
    const handleOffline = () => setIsOffline(true)

    window.addEventListener('online', handleOnline)
    window.addEventListener('offline', handleOffline)

    return () => {
      window.removeEventListener('online', handleOnline)
      window.removeEventListener('offline', handleOffline)
    }
  }, [])

  const handleRetry = async () => {
    setIsRetrying(true)
    try {
      await fetch('/api/health', { method: 'HEAD' })
      setIsOffline(false)
    } catch {
      // Still offline
    } finally {
      setIsRetrying(false)
    }
  }

  if (!isOffline) return null

  return (
    <div className="fixed bottom-0 inset-x-0 z-50 bg-gray-900 text-white px-4 py-3 shadow-lg">
      <div className="max-w-7xl mx-auto flex items-center justify-between gap-4">
        <div className="flex items-center gap-3">
          <WifiOff className="w-5 h-5 text-gray-400" />
          <p className="text-body-sm">
            You're currently offline. Some features may be unavailable.
          </p>
        </div>
        <button
          onClick={handleRetry}
          disabled={isRetrying}
          className="flex items-center gap-2 px-4 py-2 bg-white text-gray-900 rounded-full text-body-sm font-medium hover:bg-gray-100 disabled:opacity-50 transition-colors"
        >
          <RefreshCw className={`w-4 h-4 ${isRetrying ? 'animate-spin' : ''}`} />
          {isRetrying ? 'Checking...' : 'Retry'}
        </button>
      </div>
    </div>
  )
}
```

### Inline Loading Error

Error state within a loading card or section.

```tsx
import { AlertCircle, RefreshCw } from 'lucide-react'

interface InlineErrorProps {
  message?: string
  onRetry?: () => void
  retrying?: boolean
}

export function InlineError({
  message = 'Something went wrong',
  onRetry,
  retrying,
}: InlineErrorProps) {
  return (
    <div className="flex flex-col items-center justify-center py-8 px-4 text-center">
      <div className="w-12 h-12 bg-red-50 rounded-full flex items-center justify-center mb-4">
        <AlertCircle className="w-6 h-6 text-mereka-burgundy" />
      </div>
      <p className="text-body text-gray-700 mb-4">{message}</p>
      {onRetry && (
        <button
          onClick={onRetry}
          disabled={retrying}
          className="inline-flex items-center gap-2 px-4 py-2 bg-gray-900 text-white rounded-full text-body-sm font-medium hover:bg-gray-800 disabled:opacity-50 transition-colors"
        >
          <RefreshCw className={`w-4 h-4 ${retrying ? 'animate-spin' : ''}`} />
          {retrying ? 'Retrying...' : 'Try again'}
        </button>
      )}
    </div>
  )
}
```

---

## 3. 404 Not Found Page

```tsx
import { Search, Home, ArrowLeft } from 'lucide-react'
import Link from 'next/link'
import { useRouter } from 'next/router'

export function NotFoundPage() {
  const router = useRouter()

  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center px-4">
      <div className="max-w-lg w-full text-center">
        {/* Illustration */}
        <div className="relative w-64 h-64 mx-auto mb-8">
          <div className="absolute inset-0 bg-gradient-to-br from-mereka-teal/20 to-mereka-magenta/20 rounded-full" />
          <div className="absolute inset-4 bg-white rounded-full shadow-lg flex items-center justify-center">
            <span className="font-heading text-[80px] text-mereka-burgundy">404</span>
          </div>
        </div>

        {/* Content */}
        <h1 className="font-heading text-h1 text-gray-900 mb-4">
          Page not found
        </h1>
        <p className="text-body text-gray-600 mb-8">
          Sorry, we couldn't find the page you're looking for. It might have been
          moved or deleted.
        </p>

        {/* Actions */}
        <div className="flex flex-col sm:flex-row items-center justify-center gap-3">
          <button
            onClick={() => router.back()}
            className="inline-flex items-center gap-2 px-6 py-3 border border-gray-300 text-gray-700 rounded-full font-medium hover:bg-gray-100 transition-colors"
          >
            <ArrowLeft className="w-5 h-5" />
            Go back
          </button>
          <Link
            href="/"
            className="inline-flex items-center gap-2 px-6 py-3 bg-gray-900 text-white rounded-full font-medium hover:bg-gray-800 transition-colors"
          >
            <Home className="w-5 h-5" />
            Back to home
          </Link>
        </div>

        {/* Search suggestion */}
        <div className="mt-12 pt-8 border-t border-gray-200">
          <p className="text-body-sm text-gray-500 mb-4">
            Try searching for what you need
          </p>
          <div className="relative max-w-md mx-auto">
            <Search className="absolute left-4 top-1/2 -translate-y-1/2 w-5 h-5 text-gray-400" />
            <input
              type="search"
              placeholder="Search experiences, hosts, locations..."
              className="w-full h-12 pl-12 pr-4 rounded-full border border-gray-300 focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal"
            />
          </div>
        </div>
      </div>
    </div>
  )
}
```

---

## 4. 500 Server Error Page

```tsx
import { ServerCrash, RefreshCw, Home, Mail } from 'lucide-react'
import Link from 'next/link'

interface ServerErrorPageProps {
  errorId?: string
}

export function ServerErrorPage({ errorId }: ServerErrorPageProps) {
  const handleRefresh = () => {
    window.location.reload()
  }

  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center px-4">
      <div className="max-w-lg w-full text-center">
        {/* Illustration */}
        <div className="relative w-64 h-64 mx-auto mb-8">
          <div className="absolute inset-0 bg-gradient-to-br from-mereka-burgundy/20 to-mereka-orange/20 rounded-full animate-pulse" />
          <div className="absolute inset-4 bg-white rounded-full shadow-lg flex items-center justify-center">
            <ServerCrash className="w-20 h-20 text-mereka-burgundy" />
          </div>
        </div>

        {/* Content */}
        <h1 className="font-heading text-h1 text-gray-900 mb-4">
          Something went wrong
        </h1>
        <p className="text-body text-gray-600 mb-8">
          We're experiencing technical difficulties. Our team has been notified
          and is working on a fix.
        </p>

        {/* Error ID */}
        {errorId && (
          <div className="bg-gray-100 rounded-lg px-4 py-3 mb-8 inline-block">
            <p className="text-caption text-gray-500">Error Reference</p>
            <code className="text-body-sm font-mono text-gray-700">{errorId}</code>
          </div>
        )}

        {/* Actions */}
        <div className="flex flex-col sm:flex-row items-center justify-center gap-3">
          <button
            onClick={handleRefresh}
            className="inline-flex items-center gap-2 px-6 py-3 bg-gray-900 text-white rounded-full font-medium hover:bg-gray-800 transition-colors"
          >
            <RefreshCw className="w-5 h-5" />
            Try again
          </button>
          <Link
            href="/"
            className="inline-flex items-center gap-2 px-6 py-3 border border-gray-300 text-gray-700 rounded-full font-medium hover:bg-gray-100 transition-colors"
          >
            <Home className="w-5 h-5" />
            Back to home
          </Link>
        </div>

        {/* Support link */}
        <div className="mt-12 pt-8 border-t border-gray-200">
          <p className="text-body-sm text-gray-500 mb-3">
            Need help? Contact our support team
          </p>
          <a
            href="mailto:support@mereka.io"
            className="inline-flex items-center gap-2 text-mereka-teal hover:underline"
          >
            <Mail className="w-4 h-4" />
            support@mereka.io
          </a>
        </div>
      </div>
    </div>
  )
}
```

---

## 5. 403 Forbidden / Access Denied Page

```tsx
import { ShieldX, LogIn, Home, ArrowLeft } from 'lucide-react'
import Link from 'next/link'
import { useRouter } from 'next/router'

interface ForbiddenPageProps {
  reason?: 'auth' | 'permission' | 'subscription'
}

export function ForbiddenPage({ reason = 'permission' }: ForbiddenPageProps) {
  const router = useRouter()

  const content = {
    auth: {
      title: 'Sign in required',
      message: 'You need to sign in to access this page.',
      primaryAction: { label: 'Sign in', href: '/login', icon: LogIn },
    },
    permission: {
      title: 'Access denied',
      message: "You don't have permission to view this page. If you think this is a mistake, contact the owner.",
      primaryAction: { label: 'Go back', onClick: () => router.back(), icon: ArrowLeft },
    },
    subscription: {
      title: 'Upgrade required',
      message: 'This feature requires a higher subscription plan.',
      primaryAction: { label: 'View plans', href: '/pricing', icon: null },
    },
  }

  const { title, message, primaryAction } = content[reason]

  return (
    <div className="min-h-screen bg-gray-50 flex items-center justify-center px-4">
      <div className="max-w-lg w-full text-center">
        {/* Illustration */}
        <div className="relative w-64 h-64 mx-auto mb-8">
          <div className="absolute inset-0 bg-gradient-to-br from-mereka-burgundy/20 to-gray-200 rounded-full" />
          <div className="absolute inset-4 bg-white rounded-full shadow-lg flex items-center justify-center">
            <ShieldX className="w-20 h-20 text-mereka-burgundy" />
          </div>
        </div>

        {/* Content */}
        <h1 className="font-heading text-h1 text-gray-900 mb-4">{title}</h1>
        <p className="text-body text-gray-600 mb-8">{message}</p>

        {/* Actions */}
        <div className="flex flex-col sm:flex-row items-center justify-center gap-3">
          {primaryAction.href ? (
            <Link
              href={primaryAction.href}
              className="inline-flex items-center gap-2 px-6 py-3 bg-gray-900 text-white rounded-full font-medium hover:bg-gray-800 transition-colors"
            >
              {primaryAction.icon && <primaryAction.icon className="w-5 h-5" />}
              {primaryAction.label}
            </Link>
          ) : (
            <button
              onClick={primaryAction.onClick}
              className="inline-flex items-center gap-2 px-6 py-3 bg-gray-900 text-white rounded-full font-medium hover:bg-gray-800 transition-colors"
            >
              {primaryAction.icon && <primaryAction.icon className="w-5 h-5" />}
              {primaryAction.label}
            </button>
          )}
          <Link
            href="/"
            className="inline-flex items-center gap-2 px-6 py-3 border border-gray-300 text-gray-700 rounded-full font-medium hover:bg-gray-100 transition-colors"
          >
            <Home className="w-5 h-5" />
            Back to home
          </Link>
        </div>
      </div>
    </div>
  )
}
```

---

## 6. Empty Search Results

```tsx
import { Search, SlidersHorizontal, X } from 'lucide-react'

interface EmptySearchResultsProps {
  query: string
  filters?: { label: string; value: string }[]
  onClearFilters?: () => void
  suggestions?: string[]
  onSuggestionClick?: (suggestion: string) => void
}

export function EmptySearchResults({
  query,
  filters = [],
  onClearFilters,
  suggestions = [],
  onSuggestionClick,
}: EmptySearchResultsProps) {
  return (
    <div className="flex flex-col items-center justify-center py-16 px-4 text-center">
      {/* Illustration */}
      <div className="w-24 h-24 bg-gray-100 rounded-full flex items-center justify-center mb-6">
        <Search className="w-10 h-10 text-gray-400" />
      </div>

      {/* Content */}
      <h2 className="font-heading text-h3 text-gray-900 mb-2">
        No results found
      </h2>
      <p className="text-body text-gray-600 max-w-md mb-6">
        We couldn't find any experiences matching{' '}
        <span className="font-medium text-gray-900">"{query}"</span>
        {filters.length > 0 && ' with your current filters'}.
      </p>

      {/* Active Filters */}
      {filters.length > 0 && (
        <div className="flex flex-wrap items-center justify-center gap-2 mb-6">
          {filters.map((filter, index) => (
            <span
              key={index}
              className="inline-flex items-center gap-1 px-3 py-1 bg-gray-100 rounded-full text-body-sm text-gray-700"
            >
              {filter.label}: {filter.value}
            </span>
          ))}
          <button
            onClick={onClearFilters}
            className="inline-flex items-center gap-1 px-3 py-1 text-mereka-burgundy hover:bg-red-50 rounded-full text-body-sm font-medium transition-colors"
          >
            <X className="w-4 h-4" />
            Clear all
          </button>
        </div>
      )}

      {/* Suggestions */}
      {suggestions.length > 0 && (
        <div className="mt-4">
          <p className="text-body-sm text-gray-500 mb-3">Try searching for:</p>
          <div className="flex flex-wrap items-center justify-center gap-2">
            {suggestions.map((suggestion, index) => (
              <button
                key={index}
                onClick={() => onSuggestionClick?.(suggestion)}
                className="px-4 py-2 bg-white border border-gray-200 rounded-full text-body-sm text-gray-700 hover:border-mereka-teal hover:text-mereka-teal transition-colors"
              >
                {suggestion}
              </button>
            ))}
          </div>
        </div>
      )}

      {/* Tips */}
      <div className="mt-8 p-4 bg-gray-50 rounded-xl max-w-md">
        <div className="flex items-center gap-2 mb-2">
          <SlidersHorizontal className="w-4 h-4 text-gray-500" />
          <span className="text-body-sm font-medium text-gray-700">Search tips</span>
        </div>
        <ul className="text-body-sm text-gray-600 text-left space-y-1">
          <li>Check for typos in your search term</li>
          <li>Try using more general keywords</li>
          <li>Remove some filters to see more results</li>
        </ul>
      </div>
    </div>
  )
}
```

### Generic Empty State

```tsx
import { Inbox, Plus } from 'lucide-react'

interface EmptyStateProps {
  icon?: React.ReactNode
  title: string
  description: string
  action?: {
    label: string
    onClick: () => void
    icon?: React.ReactNode
  }
}

export function EmptyState({
  icon,
  title,
  description,
  action,
}: EmptyStateProps) {
  return (
    <div className="flex flex-col items-center justify-center py-16 px-4 text-center">
      <div className="w-20 h-20 bg-gray-100 rounded-full flex items-center justify-center mb-6">
        {icon || <Inbox className="w-8 h-8 text-gray-400" />}
      </div>

      <h3 className="font-heading text-h4 text-gray-900 mb-2">{title}</h3>
      <p className="text-body text-gray-600 max-w-md mb-6">{description}</p>

      {action && (
        <button
          onClick={action.onClick}
          className="inline-flex items-center gap-2 px-6 py-3 bg-gray-900 text-white rounded-full font-medium hover:bg-gray-800 transition-colors"
        >
          {action.icon || <Plus className="w-5 h-5" />}
          {action.label}
        </button>
      )}
    </div>
  )
}
```

---

## 7. Failed Payment States

### Payment Failed Modal

```tsx
import { XCircle, CreditCard, RefreshCw, HelpCircle } from 'lucide-react'

interface PaymentFailedModalProps {
  isOpen: boolean
  onClose: () => void
  onRetry: () => void
  onChangePayment: () => void
  error?: {
    code: string
    message: string
  }
  amount: {
    value: number
    currency: string
  }
}

export function PaymentFailedModal({
  isOpen,
  onClose,
  onRetry,
  onChangePayment,
  error,
  amount,
}: PaymentFailedModalProps) {
  if (!isOpen) return null

  const getErrorMessage = (code: string) => {
    const messages: Record<string, string> = {
      card_declined: 'Your card was declined. Please try a different payment method.',
      insufficient_funds: 'Insufficient funds. Please use a different card.',
      expired_card: 'Your card has expired. Please update your payment method.',
      incorrect_cvc: 'The security code is incorrect. Please check and try again.',
      processing_error: 'There was an error processing your payment. Please try again.',
      default: 'Payment could not be completed. Please try again or use a different method.',
    }
    return messages[code] || messages.default
  }

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-4">
      {/* Backdrop */}
      <div
        className="absolute inset-0 bg-black/50"
        onClick={onClose}
        aria-hidden="true"
      />

      {/* Modal */}
      <div className="relative bg-white rounded-2xl shadow-xl max-w-md w-full overflow-hidden">
        {/* Header */}
        <div className="bg-red-50 px-6 py-8 text-center">
          <div className="w-16 h-16 bg-white rounded-full shadow-lg flex items-center justify-center mx-auto mb-4">
            <XCircle className="w-8 h-8 text-mereka-burgundy" />
          </div>
          <h2 className="font-heading text-h3 text-gray-900">Payment failed</h2>
          <p className="text-h4 font-heading text-mereka-burgundy mt-2">
            {amount.currency} {amount.value.toLocaleString()}
          </p>
        </div>

        {/* Content */}
        <div className="px-6 py-6">
          <p className="text-body text-gray-600 text-center mb-6">
            {error ? getErrorMessage(error.code) : getErrorMessage('default')}
          </p>

          {/* Actions */}
          <div className="space-y-3">
            <button
              onClick={onRetry}
              className="w-full inline-flex items-center justify-center gap-2 px-6 py-3 bg-gray-900 text-white rounded-full font-medium hover:bg-gray-800 transition-colors"
            >
              <RefreshCw className="w-5 h-5" />
              Try again
            </button>
            <button
              onClick={onChangePayment}
              className="w-full inline-flex items-center justify-center gap-2 px-6 py-3 border border-gray-300 text-gray-700 rounded-full font-medium hover:bg-gray-100 transition-colors"
            >
              <CreditCard className="w-5 h-5" />
              Use different payment method
            </button>
          </div>

          {/* Help link */}
          <div className="mt-6 text-center">
            <a
              href="/help/payments"
              className="inline-flex items-center gap-1 text-body-sm text-gray-500 hover:text-mereka-teal"
            >
              <HelpCircle className="w-4 h-4" />
              Need help with payments?
            </a>
          </div>
        </div>
      </div>
    </div>
  )
}
```

### Inline Payment Error

```tsx
import { AlertCircle, CreditCard } from 'lucide-react'

interface PaymentErrorBannerProps {
  message: string
  onRetry?: () => void
  onUpdatePayment?: () => void
}

export function PaymentErrorBanner({
  message,
  onRetry,
  onUpdatePayment,
}: PaymentErrorBannerProps) {
  return (
    <div className="bg-red-50 border border-mereka-burgundy/20 rounded-xl p-4">
      <div className="flex items-start gap-3">
        <AlertCircle className="w-5 h-5 text-mereka-burgundy flex-shrink-0 mt-0.5" />
        <div className="flex-1">
          <p className="text-body-sm text-mereka-burgundy font-medium">
            Payment failed
          </p>
          <p className="text-body-sm text-gray-600 mt-1">{message}</p>
          <div className="flex items-center gap-4 mt-3">
            {onRetry && (
              <button
                onClick={onRetry}
                className="text-body-sm font-medium text-mereka-teal hover:underline"
              >
                Try again
              </button>
            )}
            {onUpdatePayment && (
              <button
                onClick={onUpdatePayment}
                className="inline-flex items-center gap-1 text-body-sm font-medium text-gray-600 hover:text-gray-900"
              >
                <CreditCard className="w-4 h-4" />
                Update payment method
              </button>
            )}
          </div>
        </div>
      </div>
    </div>
  )
}
```

---

## 8. Session Expired

```tsx
import { Clock, LogIn, X } from 'lucide-react'
import { useRouter } from 'next/router'

interface SessionExpiredModalProps {
  isOpen: boolean
  onClose?: () => void
  returnUrl?: string
}

export function SessionExpiredModal({
  isOpen,
  onClose,
  returnUrl,
}: SessionExpiredModalProps) {
  const router = useRouter()

  if (!isOpen) return null

  const handleLogin = () => {
    const url = returnUrl
      ? `/login?returnUrl=${encodeURIComponent(returnUrl)}`
      : '/login'
    router.push(url)
  }

  return (
    <div className="fixed inset-0 z-50 flex items-center justify-center p-4">
      {/* Backdrop */}
      <div className="absolute inset-0 bg-black/50" aria-hidden="true" />

      {/* Modal */}
      <div
        className="relative bg-white rounded-2xl shadow-xl max-w-sm w-full p-6 text-center"
        role="alertdialog"
        aria-labelledby="session-expired-title"
        aria-describedby="session-expired-description"
      >
        {onClose && (
          <button
            onClick={onClose}
            className="absolute top-4 right-4 p-1 rounded-full hover:bg-gray-100 transition-colors"
            aria-label="Close"
          >
            <X className="w-5 h-5 text-gray-400" />
          </button>
        )}

        {/* Icon */}
        <div className="w-16 h-16 bg-mereka-orange/10 rounded-full flex items-center justify-center mx-auto mb-4">
          <Clock className="w-8 h-8 text-mereka-orange" />
        </div>

        {/* Content */}
        <h2
          id="session-expired-title"
          className="font-heading text-h3 text-gray-900 mb-2"
        >
          Session expired
        </h2>
        <p
          id="session-expired-description"
          className="text-body text-gray-600 mb-6"
        >
          Your session has expired for security reasons. Please sign in again to
          continue.
        </p>

        {/* Action */}
        <button
          onClick={handleLogin}
          className="w-full inline-flex items-center justify-center gap-2 px-6 py-3 bg-gray-900 text-white rounded-full font-medium hover:bg-gray-800 transition-colors"
        >
          <LogIn className="w-5 h-5" />
          Sign in again
        </button>

        {/* Note */}
        <p className="text-caption text-gray-500 mt-4">
          Don't worry, your data has been saved.
        </p>
      </div>
    </div>
  )
}
```

### Session Expiring Warning Toast

```tsx
import { Clock, RefreshCw } from 'lucide-react'
import { useState, useEffect } from 'react'

interface SessionExpiringToastProps {
  expiresIn: number // seconds
  onExtend: () => void
}

export function SessionExpiringToast({
  expiresIn,
  onExtend,
}: SessionExpiringToastProps) {
  const [timeLeft, setTimeLeft] = useState(expiresIn)
  const [extending, setExtending] = useState(false)

  useEffect(() => {
    const timer = setInterval(() => {
      setTimeLeft((prev) => Math.max(0, prev - 1))
    }, 1000)

    return () => clearInterval(timer)
  }, [])

  const handleExtend = async () => {
    setExtending(true)
    await onExtend()
    setExtending(false)
  }

  const formatTime = (seconds: number) => {
    const mins = Math.floor(seconds / 60)
    const secs = seconds % 60
    return `${mins}:${secs.toString().padStart(2, '0')}`
  }

  return (
    <div className="bg-mereka-orange/10 border border-mereka-orange/30 rounded-xl p-4">
      <div className="flex items-start gap-3">
        <Clock className="w-5 h-5 text-mereka-orange flex-shrink-0 mt-0.5" />
        <div className="flex-1">
          <p className="text-body-sm font-medium text-gray-900">
            Session expiring soon
          </p>
          <p className="text-body-sm text-gray-600 mt-1">
            Your session will expire in{' '}
            <span className="font-medium text-mereka-orange">
              {formatTime(timeLeft)}
            </span>
          </p>
          <button
            onClick={handleExtend}
            disabled={extending}
            className="inline-flex items-center gap-1 mt-2 text-body-sm font-medium text-mereka-teal hover:underline disabled:opacity-50"
          >
            <RefreshCw className={`w-4 h-4 ${extending ? 'animate-spin' : ''}`} />
            {extending ? 'Extending...' : 'Extend session'}
          </button>
        </div>
      </div>
    </div>
  )
}
```

---

## 9. Error Boundaries for React

### Basic Error Boundary

```tsx
import React from 'react'
import { AlertTriangle, RefreshCw, Home } from 'lucide-react'

interface ErrorBoundaryProps {
  children: React.ReactNode
  fallback?: React.ReactNode
  onError?: (error: Error, errorInfo: React.ErrorInfo) => void
}

interface ErrorBoundaryState {
  hasError: boolean
  error?: Error
}

export class ErrorBoundary extends React.Component<
  ErrorBoundaryProps,
  ErrorBoundaryState
> {
  constructor(props: ErrorBoundaryProps) {
    super(props)
    this.state = { hasError: false }
  }

  static getDerivedStateFromError(error: Error): ErrorBoundaryState {
    return { hasError: true, error }
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    console.error('Error caught by boundary:', error, errorInfo)
    this.props.onError?.(error, errorInfo)
  }

  handleRetry = () => {
    this.setState({ hasError: false, error: undefined })
  }

  render() {
    if (this.state.hasError) {
      if (this.props.fallback) {
        return this.props.fallback
      }

      return <DefaultErrorFallback error={this.state.error} onRetry={this.handleRetry} />
    }

    return this.props.children
  }
}

interface DefaultErrorFallbackProps {
  error?: Error
  onRetry?: () => void
}

function DefaultErrorFallback({ error, onRetry }: DefaultErrorFallbackProps) {
  return (
    <div className="min-h-[400px] flex items-center justify-center p-8">
      <div className="max-w-md w-full text-center">
        <div className="w-16 h-16 bg-red-50 rounded-full flex items-center justify-center mx-auto mb-4">
          <AlertTriangle className="w-8 h-8 text-mereka-burgundy" />
        </div>

        <h2 className="font-heading text-h3 text-gray-900 mb-2">
          Something went wrong
        </h2>
        <p className="text-body text-gray-600 mb-6">
          An unexpected error occurred. Our team has been notified.
        </p>

        {process.env.NODE_ENV === 'development' && error && (
          <div className="bg-gray-100 rounded-lg p-4 mb-6 text-left overflow-auto max-h-32">
            <code className="text-caption text-mereka-burgundy font-mono">
              {error.message}
            </code>
          </div>
        )}

        <div className="flex items-center justify-center gap-3">
          {onRetry && (
            <button
              onClick={onRetry}
              className="inline-flex items-center gap-2 px-6 py-3 bg-gray-900 text-white rounded-full font-medium hover:bg-gray-800 transition-colors"
            >
              <RefreshCw className="w-5 h-5" />
              Try again
            </button>
          )}
          <a
            href="/"
            className="inline-flex items-center gap-2 px-6 py-3 border border-gray-300 text-gray-700 rounded-full font-medium hover:bg-gray-100 transition-colors"
          >
            <Home className="w-5 h-5" />
            Go home
          </a>
        </div>
      </div>
    </div>
  )
}
```

### Section-Level Error Boundary

For containing errors within specific sections.

```tsx
import { AlertCircle, RefreshCw } from 'lucide-react'
import React from 'react'

interface SectionErrorBoundaryProps {
  children: React.ReactNode
  sectionName?: string
}

interface SectionErrorBoundaryState {
  hasError: boolean
}

export class SectionErrorBoundary extends React.Component<
  SectionErrorBoundaryProps,
  SectionErrorBoundaryState
> {
  constructor(props: SectionErrorBoundaryProps) {
    super(props)
    this.state = { hasError: false }
  }

  static getDerivedStateFromError(): SectionErrorBoundaryState {
    return { hasError: true }
  }

  handleRetry = () => {
    this.setState({ hasError: false })
  }

  render() {
    if (this.state.hasError) {
      return (
        <div className="bg-red-50 border border-mereka-burgundy/20 rounded-xl p-6 text-center">
          <AlertCircle className="w-8 h-8 text-mereka-burgundy mx-auto mb-3" />
          <p className="text-body text-gray-700 mb-4">
            {this.props.sectionName
              ? `Failed to load ${this.props.sectionName}`
              : 'This section failed to load'}
          </p>
          <button
            onClick={this.handleRetry}
            className="inline-flex items-center gap-2 px-4 py-2 bg-white border border-gray-300 rounded-full text-body-sm font-medium hover:bg-gray-50 transition-colors"
          >
            <RefreshCw className="w-4 h-4" />
            Retry
          </button>
        </div>
      )
    }

    return this.props.children
  }
}
```

### Usage

```tsx
// Page-level error boundary
<ErrorBoundary
  onError={(error) => {
    // Log to error tracking service
    trackError(error)
  }}
>
  <BookingPage />
</ErrorBoundary>

// Section-level error boundary
<div className="grid grid-cols-3 gap-6">
  <SectionErrorBoundary sectionName="recommended experiences">
    <RecommendedExperiences />
  </SectionErrorBoundary>

  <SectionErrorBoundary sectionName="reviews">
    <Reviews />
  </SectionErrorBoundary>

  <SectionErrorBoundary sectionName="similar experiences">
    <SimilarExperiences />
  </SectionErrorBoundary>
</div>
```

---

## 10. Retry Patterns

### useRetry Hook

```tsx
import { useState, useCallback } from 'react'

interface UseRetryOptions {
  maxRetries?: number
  initialDelay?: number
  maxDelay?: number
  backoffMultiplier?: number
  onRetry?: (attempt: number) => void
  onMaxRetriesReached?: () => void
}

interface UseRetryReturn<T> {
  execute: (fn: () => Promise<T>) => Promise<T>
  isRetrying: boolean
  retryCount: number
  lastError: Error | null
  reset: () => void
}

export function useRetry<T>(options: UseRetryOptions = {}): UseRetryReturn<T> {
  const {
    maxRetries = 3,
    initialDelay = 1000,
    maxDelay = 30000,
    backoffMultiplier = 2,
    onRetry,
    onMaxRetriesReached,
  } = options

  const [isRetrying, setIsRetrying] = useState(false)
  const [retryCount, setRetryCount] = useState(0)
  const [lastError, setLastError] = useState<Error | null>(null)

  const sleep = (ms: number) => new Promise((resolve) => setTimeout(resolve, ms))

  const execute = useCallback(
    async (fn: () => Promise<T>): Promise<T> => {
      let currentDelay = initialDelay
      let attempts = 0

      while (attempts <= maxRetries) {
        try {
          setIsRetrying(attempts > 0)
          const result = await fn()
          setIsRetrying(false)
          setRetryCount(0)
          setLastError(null)
          return result
        } catch (error) {
          attempts++
          setRetryCount(attempts)
          setLastError(error as Error)

          if (attempts > maxRetries) {
            setIsRetrying(false)
            onMaxRetriesReached?.()
            throw error
          }

          onRetry?.(attempts)
          await sleep(currentDelay)
          currentDelay = Math.min(currentDelay * backoffMultiplier, maxDelay)
        }
      }

      throw lastError
    },
    [maxRetries, initialDelay, maxDelay, backoffMultiplier, onRetry, onMaxRetriesReached]
  )

  const reset = useCallback(() => {
    setIsRetrying(false)
    setRetryCount(0)
    setLastError(null)
  }, [])

  return { execute, isRetrying, retryCount, lastError, reset }
}
```

### Retry Button with Backoff

```tsx
import { RefreshCw } from 'lucide-react'
import { useState, useEffect } from 'react'

interface RetryButtonProps {
  onRetry: () => Promise<void>
  initialDelay?: number
  maxRetries?: number
  disabled?: boolean
}

export function RetryButton({
  onRetry,
  initialDelay = 5,
  maxRetries = 3,
  disabled,
}: RetryButtonProps) {
  const [countdown, setCountdown] = useState(0)
  const [retryCount, setRetryCount] = useState(0)
  const [isRetrying, setIsRetrying] = useState(false)

  useEffect(() => {
    if (countdown > 0) {
      const timer = setTimeout(() => setCountdown(countdown - 1), 1000)
      return () => clearTimeout(timer)
    }
  }, [countdown])

  const handleRetry = async () => {
    if (retryCount >= maxRetries) return

    setIsRetrying(true)
    try {
      await onRetry()
      setRetryCount(0)
    } catch {
      const newRetryCount = retryCount + 1
      setRetryCount(newRetryCount)

      if (newRetryCount < maxRetries) {
        // Exponential backoff
        const delay = initialDelay * Math.pow(2, newRetryCount - 1)
        setCountdown(delay)
      }
    } finally {
      setIsRetrying(false)
    }
  }

  const isDisabled = disabled || isRetrying || countdown > 0 || retryCount >= maxRetries

  return (
    <button
      onClick={handleRetry}
      disabled={isDisabled}
      className="inline-flex items-center gap-2 px-4 py-2 bg-gray-900 text-white rounded-full text-body-sm font-medium hover:bg-gray-800 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
    >
      <RefreshCw className={`w-4 h-4 ${isRetrying ? 'animate-spin' : ''}`} />
      {isRetrying
        ? 'Retrying...'
        : countdown > 0
        ? `Retry in ${countdown}s`
        : retryCount >= maxRetries
        ? 'Max retries reached'
        : 'Retry'}
    </button>
  )
}
```

### Auto-Retry Component

```tsx
import { RefreshCw, AlertCircle } from 'lucide-react'
import { useState, useEffect, useCallback } from 'react'

interface AutoRetryProps {
  fetchFn: () => Promise<any>
  maxRetries?: number
  retryDelay?: number
  children: (data: any) => React.ReactNode
  loadingComponent?: React.ReactNode
  errorComponent?: (error: Error, retry: () => void) => React.ReactNode
}

export function AutoRetry({
  fetchFn,
  maxRetries = 3,
  retryDelay = 2000,
  children,
  loadingComponent,
  errorComponent,
}: AutoRetryProps) {
  const [data, setData] = useState<any>(null)
  const [error, setError] = useState<Error | null>(null)
  const [loading, setLoading] = useState(true)
  const [retryCount, setRetryCount] = useState(0)

  const execute = useCallback(async () => {
    setLoading(true)
    setError(null)

    try {
      const result = await fetchFn()
      setData(result)
      setRetryCount(0)
    } catch (err) {
      const newRetryCount = retryCount + 1

      if (newRetryCount <= maxRetries) {
        setRetryCount(newRetryCount)
        setTimeout(execute, retryDelay * newRetryCount)
      } else {
        setError(err as Error)
      }
    } finally {
      setLoading(false)
    }
  }, [fetchFn, maxRetries, retryDelay, retryCount])

  useEffect(() => {
    execute()
  }, [])

  const retry = () => {
    setRetryCount(0)
    execute()
  }

  if (loading && !data) {
    return (
      loadingComponent || (
        <div className="flex items-center justify-center py-8">
          <RefreshCw className="w-6 h-6 text-gray-400 animate-spin" />
          {retryCount > 0 && (
            <span className="ml-2 text-body-sm text-gray-500">
              Retry attempt {retryCount}/{maxRetries}
            </span>
          )}
        </div>
      )
    )
  }

  if (error) {
    return (
      errorComponent?.(error, retry) || (
        <div className="flex flex-col items-center justify-center py-8 text-center">
          <AlertCircle className="w-8 h-8 text-mereka-burgundy mb-3" />
          <p className="text-body text-gray-700 mb-4">Failed to load data</p>
          <button
            onClick={retry}
            className="inline-flex items-center gap-2 px-4 py-2 bg-gray-900 text-white rounded-full text-body-sm font-medium hover:bg-gray-800 transition-colors"
          >
            <RefreshCw className="w-4 h-4" />
            Try again
          </button>
        </div>
      )
    )
  }

  return <>{children(data)}</>
}
```

### Usage Examples

```tsx
// Using useRetry hook
function BookingPage() {
  const { execute, isRetrying, retryCount } = useRetry({
    maxRetries: 3,
    onRetry: (attempt) => {
      console.log(`Retry attempt ${attempt}`)
    },
  })

  const handleSubmit = async () => {
    try {
      await execute(() => api.createBooking(data))
      showSuccess('Booking created!')
    } catch (error) {
      showError('Failed to create booking after multiple attempts')
    }
  }

  return (
    <button onClick={handleSubmit} disabled={isRetrying}>
      {isRetrying ? `Retrying (${retryCount}/3)...` : 'Book Now'}
    </button>
  )
}

// Using AutoRetry component
<AutoRetry
  fetchFn={() => api.getExperiences()}
  maxRetries={3}
  retryDelay={2000}
>
  {(experiences) => (
    <ExperienceGrid experiences={experiences} />
  )}
</AutoRetry>

// Using RetryButton
<div className="text-center">
  <p className="text-body text-gray-600 mb-4">
    Failed to load content
  </p>
  <RetryButton
    onRetry={loadContent}
    maxRetries={3}
    initialDelay={5}
  />
</div>
```

---

## Error Message Guidelines

### Writing Good Error Messages

| Bad | Good |
|-----|------|
| "Error" | "Unable to save your changes. Please try again." |
| "Invalid input" | "Email address must include @ symbol" |
| "Failed" | "Payment declined. Please check your card details." |
| "Something went wrong" | "We couldn't load your bookings. Tap to retry." |
| "403" | "You don't have permission to access this page" |

### Error Message Structure

```
[What happened] + [Why it happened (if known)] + [What to do next]
```

Examples:
- "Your session has expired. Please sign in again to continue."
- "We couldn't complete the payment. Your card may have been declined. Try a different payment method."
- "This page doesn't exist. It may have been moved or deleted. Search for what you need."

### Tone Guidelines

- Be empathetic, not blaming
- Be specific, not vague
- Provide a clear next step
- Avoid technical jargon
- Use sentence case, not ALL CAPS
- Never use exclamation marks for errors

---

## Accessibility

### ARIA Attributes

```tsx
// Error summary
<div role="alert" aria-live="polite">
  <ErrorSummary errors={errors} />
</div>

// Inline field error
<input
  aria-invalid={!!error}
  aria-describedby={error ? `${id}-error` : undefined}
/>
<p id={`${id}-error`} role="alert">
  {error}
</p>

// Error page
<main role="main" aria-labelledby="error-title">
  <h1 id="error-title">Page not found</h1>
</main>
```

### Focus Management

```tsx
// Focus first error field on form submission
const handleSubmit = (e: React.FormEvent) => {
  e.preventDefault()
  const errors = validate(values)

  if (Object.keys(errors).length > 0) {
    const firstErrorField = Object.keys(errors)[0]
    const element = document.getElementById(firstErrorField)
    element?.focus()
    element?.scrollIntoView({ behavior: 'smooth', block: 'center' })
  }
}
```

### Screen Reader Announcements

```tsx
// Live region for dynamic errors
<div
  aria-live="polite"
  aria-atomic="true"
  className="sr-only"
>
  {errorMessage}
</div>
```

---

## Animation & Transitions

### Error Shake Animation

```css
@keyframes error-shake {
  0%, 100% { transform: translateX(0); }
  10%, 30%, 50%, 70%, 90% { transform: translateX(-4px); }
  20%, 40%, 60%, 80% { transform: translateX(4px); }
}

.error-shake {
  animation: error-shake 0.5s ease-in-out;
}
```

### Error Fade In

```tsx
// Tailwind animation
<p className="animate-in fade-in slide-in-from-top-1 duration-200 text-mereka-burgundy">
  {error}
</p>
```

---

## Don'ts

- **Don't use red for everything** - Reserve error color for actual errors
- **Don't show errors before interaction** - Wait for user input
- **Don't clear the form on error** - Preserve user's data
- **Don't use technical jargon** - "Error 500" means nothing to users
- **Don't blame the user** - "Invalid input" vs "Please enter a valid email"
- **Don't hide errors** - Make them visible and actionable
- **Don't use generic messages** - Be specific about what went wrong
- **Don't forget recovery** - Always provide a path forward
- **Don't auto-dismiss important errors** - Let users dismiss manually
- **Don't use error modals for minor issues** - Inline errors are better

---

## Quick Import Reference

```tsx
import {
  // Status icons
  AlertCircle,
  AlertTriangle,
  XCircle,
  X,

  // Action icons
  RefreshCw,
  Home,
  ArrowLeft,
  LogIn,

  // Context icons
  Search,
  WifiOff,
  Clock,
  ServerCrash,
  ShieldX,
  CreditCard,
  HelpCircle,
  Mail,
  SlidersHorizontal,
  Inbox,
  Plus,
} from 'lucide-react'
```

---

## Related Documentation

- [Notifications](./notifications.md) - Toast messages for errors
- [Dialogs](./dialogs.md) - Error modal patterns
- [Inputs](./inputs.md) - Form field error states
- [Colors](./colors.md) - Error color system
- [Loading](./loading.md) - Loading states that precede errors
