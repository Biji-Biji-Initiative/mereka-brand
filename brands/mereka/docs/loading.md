# Loading States

> Loading states provide visual feedback during asynchronous operations. The Mereka design system includes **skeleton components**, **spinners**, **progress indicators**, and **shimmer animations** to maintain user engagement and perceived performance.

**Figma Section**: Loading States, Indicators

---

## Loading State Architecture

```
Loading Feedback
├── Skeleton Loaders (known content shape)
│   ├── Text Skeleton
│   ├── Avatar Skeleton
│   ├── Card Skeleton
│   ├── Table Row Skeleton
│   └── Form Skeleton
├── Spinners (unknown duration)
│   ├── Button Loading
│   ├── Page Spinner
│   └── Inline Indicator
├── Progress Indicators (known duration)
│   ├── Upload Progress Bar
│   └── Multi-Step Progress
└── Shimmer Animation (visual polish)
```

---

## When to Use What

| Scenario | Component | Rationale |
|----------|-----------|-----------|
| Loading a list of cards | Skeleton | Known layout, reduces perceived wait time |
| Loading user profile | Skeleton | Known content shape |
| Submitting a form | Button Spinner | Unknown duration, discrete action |
| Initial page load | Page Spinner | Unknown duration, no content to skeleton |
| File upload | Progress Bar | Known duration, shows progress |
| Multi-step wizard | Step Progress | Known steps, shows completion |
| Inline data fetch | Inline Indicator | Minimal disruption, quick operation |

### Decision Tree

```
Is the content shape known?
├── Yes → Use Skeleton
│   └── Does it have a shimmer effect?
│       └── Yes → Add shimmer animation
└── No → Is the duration known?
    ├── Yes → Use Progress Bar
    └── No → Use Spinner
```

---

## 1. Skeleton Components

Skeleton loaders mimic the structure of content that will appear, reducing perceived loading time.

### Base Skeleton Styles

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const skeletonVariants = cva(
  'bg-gray-200 rounded',
  {
    variants: {
      animation: {
        none: '',
        pulse: 'animate-pulse',
        shimmer: 'relative overflow-hidden before:absolute before:inset-0 before:-translate-x-full before:animate-[shimmer_1.5s_infinite] before:bg-gradient-to-r before:from-transparent before:via-white/60 before:to-transparent',
      },
    },
    defaultVariants: {
      animation: 'pulse',
    },
  }
)

interface SkeletonProps extends VariantProps<typeof skeletonVariants> {
  className?: string
}

export function Skeleton({ animation, className }: SkeletonProps) {
  return <div className={skeletonVariants({ animation, className })} />
}
```

### Tailwind Configuration for Shimmer

Add to your `tailwind.config.ts`:

```typescript
// tailwind.config.ts
export default {
  theme: {
    extend: {
      keyframes: {
        shimmer: {
          '100%': { transform: 'translateX(100%)' },
        },
      },
      animation: {
        shimmer: 'shimmer 1.5s infinite',
      },
    },
  },
}
```

---

### Text Skeleton

For loading text content like titles, descriptions, and paragraphs.

#### Single Line

```tsx
interface TextSkeletonProps {
  width?: 'full' | 'lg' | 'md' | 'sm' | 'xs'
  size?: 'title' | 'body' | 'caption'
  animation?: 'none' | 'pulse' | 'shimmer'
}

const widthClasses = {
  full: 'w-full',
  lg: 'w-3/4',
  md: 'w-1/2',
  sm: 'w-1/3',
  xs: 'w-1/4',
}

const sizeClasses = {
  title: 'h-7',
  body: 'h-4',
  caption: 'h-3',
}

function TextSkeleton({
  width = 'full',
  size = 'body',
  animation = 'pulse',
}: TextSkeletonProps) {
  return (
    <Skeleton
      animation={animation}
      className={`${widthClasses[width]} ${sizeClasses[size]}`}
    />
  )
}
```

#### Multi-Line

```tsx
interface MultiLineSkeletonProps {
  lines?: number
  lastLineWidth?: 'full' | 'lg' | 'md' | 'sm' | 'xs'
  size?: 'body' | 'caption'
  gap?: 'tight' | 'normal' | 'loose'
  animation?: 'none' | 'pulse' | 'shimmer'
}

const gapClasses = {
  tight: 'space-y-1.5',
  normal: 'space-y-2',
  loose: 'space-y-3',
}

function MultiLineSkeleton({
  lines = 3,
  lastLineWidth = 'md',
  size = 'body',
  gap = 'normal',
  animation = 'pulse',
}: MultiLineSkeletonProps) {
  return (
    <div className={gapClasses[gap]}>
      {Array.from({ length: lines }).map((_, index) => (
        <TextSkeleton
          key={index}
          width={index === lines - 1 ? lastLineWidth : 'full'}
          size={size}
          animation={animation}
        />
      ))}
    </div>
  )
}
```

#### Usage Examples

```tsx
// Single line title
<TextSkeleton size="title" width="lg" />

// Full width body text
<TextSkeleton size="body" width="full" />

// Caption text
<TextSkeleton size="caption" width="sm" />

// Multi-line paragraph
<MultiLineSkeleton lines={3} lastLineWidth="md" />

// Tight spacing for descriptions
<MultiLineSkeleton lines={2} gap="tight" size="caption" />

// With shimmer effect
<TextSkeleton size="body" animation="shimmer" />
```

---

### Avatar Skeleton

For loading user avatars and profile images.

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const avatarSkeletonVariants = cva(
  'rounded-full bg-gray-200',
  {
    variants: {
      size: {
        xs: 'w-6 h-6',
        sm: 'w-9 h-9',
        md: 'w-[42px] h-[42px]',
        lg: 'w-16 h-16',
        xl: 'w-[72px] h-[72px]',
        '2xl': 'w-24 h-24',
      },
      animation: {
        none: '',
        pulse: 'animate-pulse',
        shimmer: 'relative overflow-hidden before:absolute before:inset-0 before:-translate-x-full before:animate-[shimmer_1.5s_infinite] before:bg-gradient-to-r before:from-transparent before:via-white/60 before:to-transparent',
      },
    },
    defaultVariants: {
      size: 'md',
      animation: 'pulse',
    },
  }
)

interface AvatarSkeletonProps extends VariantProps<typeof avatarSkeletonVariants> {
  className?: string
}

function AvatarSkeleton({ size, animation, className }: AvatarSkeletonProps) {
  return <div className={avatarSkeletonVariants({ size, animation, className })} />
}
```

#### Usage Examples

```tsx
// Small avatar in a list
<AvatarSkeleton size="sm" />

// Profile page avatar
<AvatarSkeleton size="2xl" />

// With shimmer
<AvatarSkeleton size="lg" animation="shimmer" />

// Avatar with text
<div className="flex items-center gap-3">
  <AvatarSkeleton size="md" />
  <div className="space-y-1.5">
    <TextSkeleton width="md" size="body" />
    <TextSkeleton width="sm" size="caption" />
  </div>
</div>
```

---

### Card Skeleton

Skeleton loaders for different card types in the Mereka platform.

#### Experience Card Skeleton

```tsx
interface ExperienceCardSkeletonProps {
  animation?: 'none' | 'pulse' | 'shimmer'
}

function ExperienceCardSkeleton({
  animation = 'pulse',
}: ExperienceCardSkeletonProps) {
  const animationClass = animation === 'pulse' ? 'animate-pulse' : ''
  const shimmerClass = animation === 'shimmer'
    ? 'relative overflow-hidden before:absolute before:inset-0 before:-translate-x-full before:animate-[shimmer_1.5s_infinite] before:bg-gradient-to-r before:from-transparent before:via-white/60 before:to-transparent'
    : ''

  return (
    <article className="bg-white rounded-xl shadow-md overflow-hidden">
      {/* Image placeholder */}
      <div className={`aspect-[3/2] bg-gray-200 ${animationClass} ${shimmerClass}`} />

      {/* Content */}
      <div className="p-4 space-y-3">
        {/* Category */}
        <Skeleton animation={animation} className="h-3 w-16" />

        {/* Host */}
        <div className="flex items-center gap-2">
          <AvatarSkeleton size="xs" animation={animation} />
          <Skeleton animation={animation} className="h-3 w-24" />
        </div>

        {/* Title */}
        <Skeleton animation={animation} className="h-5 w-full" />
        <Skeleton animation={animation} className="h-5 w-3/4" />

        {/* Rating */}
        <Skeleton animation={animation} className="h-4 w-20" />

        {/* Footer */}
        <div className="flex items-center justify-between pt-3 border-t border-gray-100">
          <div className="space-y-1">
            <Skeleton animation={animation} className="h-3 w-12" />
            <Skeleton animation={animation} className="h-5 w-16" />
          </div>
          <Skeleton animation={animation} className="h-4 w-20" />
        </div>
      </div>
    </article>
  )
}
```

#### Hub Card Skeleton

```tsx
interface HubCardSkeletonProps {
  animation?: 'none' | 'pulse' | 'shimmer'
}

function HubCardSkeleton({
  animation = 'pulse',
}: HubCardSkeletonProps) {
  return (
    <article className="bg-white rounded-xl shadow-md p-6">
      <div className="flex gap-4">
        {/* Logo */}
        <Skeleton animation={animation} className="w-16 h-16 rounded-xl shrink-0" />

        {/* Content */}
        <div className="flex-1 space-y-3">
          {/* Name */}
          <Skeleton animation={animation} className="h-5 w-40" />

          {/* Description */}
          <div className="space-y-1.5">
            <Skeleton animation={animation} className="h-3 w-full" />
            <Skeleton animation={animation} className="h-3 w-3/4" />
          </div>

          {/* Meta */}
          <div className="flex gap-4">
            <Skeleton animation={animation} className="h-3 w-24" />
            <Skeleton animation={animation} className="h-3 w-20" />
            <Skeleton animation={animation} className="h-3 w-16" />
          </div>

          {/* Categories */}
          <div className="flex gap-2">
            <Skeleton animation={animation} className="h-6 w-20 rounded-full" />
            <Skeleton animation={animation} className="h-6 w-16 rounded-full" />
            <Skeleton animation={animation} className="h-6 w-24 rounded-full" />
          </div>
        </div>
      </div>
    </article>
  )
}
```

#### Expert Card Skeleton

```tsx
interface ExpertCardSkeletonProps {
  animation?: 'none' | 'pulse' | 'shimmer'
}

function ExpertCardSkeleton({
  animation = 'pulse',
}: ExpertCardSkeletonProps) {
  return (
    <article className="bg-white rounded-xl shadow-md overflow-hidden">
      {/* Header with avatar */}
      <div className="p-6 pb-4">
        <div className="flex items-start gap-4">
          {/* Avatar */}
          <AvatarSkeleton size="xl" animation={animation} />

          {/* Info */}
          <div className="flex-1 space-y-2">
            <Skeleton animation={animation} className="h-5 w-32" />
            <Skeleton animation={animation} className="h-4 w-48" />
            <Skeleton animation={animation} className="h-4 w-20" />
          </div>
        </div>
      </div>

      {/* Content */}
      <div className="px-6 pb-6 space-y-4">
        {/* Bio */}
        <MultiLineSkeleton lines={2} animation={animation} />

        {/* Expertise tags */}
        <div className="flex flex-wrap gap-2">
          <Skeleton animation={animation} className="h-6 w-20 rounded-full" />
          <Skeleton animation={animation} className="h-6 w-24 rounded-full" />
          <Skeleton animation={animation} className="h-6 w-16 rounded-full" />
        </div>

        {/* Footer */}
        <div className="flex items-center justify-between pt-4 border-t border-gray-100">
          <div className="space-y-1">
            <Skeleton animation={animation} className="h-3 w-12" />
            <Skeleton animation={animation} className="h-5 w-20" />
          </div>
          <Skeleton animation={animation} className="h-6 w-24 rounded-full" />
        </div>

        {/* Button */}
        <Skeleton animation={animation} className="h-10 w-full rounded-full" />
      </div>
    </article>
  )
}
```

#### Usage Examples

```tsx
// Experience grid loading
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
  {Array.from({ length: 8 }).map((_, i) => (
    <ExperienceCardSkeleton key={i} animation="shimmer" />
  ))}
</div>

// Hub list loading
<div className="grid grid-cols-1 lg:grid-cols-2 gap-6">
  {Array.from({ length: 4 }).map((_, i) => (
    <HubCardSkeleton key={i} />
  ))}
</div>

// Expert grid loading
<div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-6">
  {Array.from({ length: 6 }).map((_, i) => (
    <ExpertCardSkeleton key={i} animation="shimmer" />
  ))}
</div>
```

---

### Table Row Skeleton

For loading table data in admin views or list displays.

```tsx
interface TableRowSkeletonProps {
  columns: number
  animation?: 'none' | 'pulse' | 'shimmer'
}

function TableRowSkeleton({
  columns,
  animation = 'pulse',
}: TableRowSkeletonProps) {
  return (
    <tr className="border-b border-gray-100">
      {Array.from({ length: columns }).map((_, index) => (
        <td key={index} className="py-4 px-4">
          <Skeleton
            animation={animation}
            className={`h-4 ${index === 0 ? 'w-32' : index === columns - 1 ? 'w-20' : 'w-24'}`}
          />
        </td>
      ))}
    </tr>
  )
}

interface TableSkeletonProps {
  columns: number
  rows?: number
  hasHeader?: boolean
  animation?: 'none' | 'pulse' | 'shimmer'
}

function TableSkeleton({
  columns,
  rows = 5,
  hasHeader = true,
  animation = 'pulse',
}: TableSkeletonProps) {
  return (
    <div className="bg-white rounded-xl border border-gray-200 overflow-hidden">
      <table className="w-full">
        {hasHeader && (
          <thead className="bg-gray-50 border-b border-gray-200">
            <tr>
              {Array.from({ length: columns }).map((_, index) => (
                <th key={index} className="py-3 px-4 text-left">
                  <Skeleton animation={animation} className="h-4 w-20" />
                </th>
              ))}
            </tr>
          </thead>
        )}
        <tbody>
          {Array.from({ length: rows }).map((_, index) => (
            <TableRowSkeleton key={index} columns={columns} animation={animation} />
          ))}
        </tbody>
      </table>
    </div>
  )
}
```

#### Usage Examples

```tsx
// Basic table skeleton
<TableSkeleton columns={5} rows={10} />

// Without header
<TableSkeleton columns={4} rows={5} hasHeader={false} />

// With shimmer
<TableSkeleton columns={6} rows={8} animation="shimmer" />
```

---

### Form Skeleton

For loading form content with inputs and labels.

```tsx
interface FormFieldSkeletonProps {
  hasLabel?: boolean
  inputHeight?: 'default' | 'textarea'
  animation?: 'none' | 'pulse' | 'shimmer'
}

function FormFieldSkeleton({
  hasLabel = true,
  inputHeight = 'default',
  animation = 'pulse',
}: FormFieldSkeletonProps) {
  return (
    <div className="space-y-1.5">
      {hasLabel && (
        <Skeleton animation={animation} className="h-4 w-24" />
      )}
      <Skeleton
        animation={animation}
        className={`w-full rounded-lg ${
          inputHeight === 'textarea' ? 'h-32' : 'h-11'
        }`}
      />
    </div>
  )
}

interface FormSkeletonProps {
  fields?: number
  hasTextarea?: boolean
  hasButton?: boolean
  animation?: 'none' | 'pulse' | 'shimmer'
}

function FormSkeleton({
  fields = 3,
  hasTextarea = false,
  hasButton = true,
  animation = 'pulse',
}: FormSkeletonProps) {
  return (
    <div className="space-y-6">
      {Array.from({ length: fields }).map((_, index) => (
        <FormFieldSkeleton key={index} animation={animation} />
      ))}
      {hasTextarea && (
        <FormFieldSkeleton inputHeight="textarea" animation={animation} />
      )}
      {hasButton && (
        <Skeleton animation={animation} className="h-12 w-full rounded-full" />
      )}
    </div>
  )
}
```

#### Usage Examples

```tsx
// Contact form skeleton
<FormSkeleton fields={2} hasTextarea />

// Login form skeleton
<FormSkeleton fields={2} />

// Profile form skeleton
<div className="space-y-6">
  <div className="flex items-center gap-4">
    <AvatarSkeleton size="2xl" />
    <Skeleton className="h-10 w-32 rounded-full" />
  </div>
  <FormSkeleton fields={4} hasButton />
</div>
```

---

## 2. Spinner / Loader

Spinners indicate an unknown duration operation is in progress.

### Spinner Component

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
    <div className="inline-flex items-center gap-2" role="status" aria-label={label}>
      <svg
        className={spinnerVariants({ size, color, className })}
        viewBox="0 0 24 24"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <circle
          className="opacity-25"
          cx="12"
          cy="12"
          r="10"
          stroke="currentColor"
          strokeWidth="4"
        />
        <path
          className="opacity-75"
          fill="currentColor"
          d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
        />
      </svg>
      <span className="sr-only">{label}</span>
    </div>
  )
}
```

---

### Button Loading State

Buttons with loading spinners for form submissions.

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const buttonVariants = cva(
  'inline-flex items-center justify-center gap-2 rounded-full font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 disabled:pointer-events-none',
  {
    variants: {
      variant: {
        solid: 'bg-gray-900 text-white hover:bg-gray-800 disabled:bg-gray-300',
        outline: 'border border-gray-900 text-gray-900 hover:bg-gray-100 disabled:border-gray-300 disabled:text-gray-300',
        ghost: 'text-gray-900 hover:bg-gray-100 disabled:text-gray-300',
      },
      size: {
        default: 'h-9 px-4 text-sm',
        large: 'h-12 px-6 text-base',
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
    VariantProps<typeof buttonVariants> {
  isLoading?: boolean
  loadingText?: string
}

function Button({
  children,
  variant,
  size,
  isLoading,
  loadingText,
  disabled,
  ...props
}: ButtonProps) {
  return (
    <button
      className={buttonVariants({ variant, size })}
      disabled={disabled || isLoading}
      {...props}
    >
      {isLoading ? (
        <>
          <Spinner size="sm" color={variant === 'solid' ? 'white' : 'current'} />
          {loadingText || children}
        </>
      ) : (
        children
      )}
    </button>
  )
}
```

#### Usage Examples

```tsx
// Default loading
<Button isLoading>
  Book Experience
</Button>

// With loading text
<Button isLoading loadingText="Booking...">
  Book Experience
</Button>

// Large button loading
<Button size="large" isLoading loadingText="Saving...">
  Save Changes
</Button>

// Outline button loading
<Button variant="outline" isLoading>
  Cancel
</Button>
```

---

### Page Loading Spinner

Full-page loading indicator for initial page loads.

```tsx
interface PageSpinnerProps {
  message?: string
  fullScreen?: boolean
}

function PageSpinner({
  message = 'Loading...',
  fullScreen = true,
}: PageSpinnerProps) {
  return (
    <div
      className={`flex flex-col items-center justify-center gap-4 ${
        fullScreen ? 'fixed inset-0 bg-white z-50' : 'py-12'
      }`}
      role="status"
      aria-label={message}
    >
      <Spinner size="xl" color="primary" />
      <p className="text-body text-gray-600 animate-pulse">{message}</p>
    </div>
  )
}
```

#### Usage Examples

```tsx
// Full page loading
<PageSpinner message="Loading your experiences..." />

// Section loading
<PageSpinner message="Loading results..." fullScreen={false} />

// With custom message
<PageSpinner message="Please wait while we find the best matches for you" />
```

---

### Inline Loading Indicator

Small loading indicator for inline operations.

```tsx
interface InlineLoaderProps {
  text?: string
  size?: 'sm' | 'md'
}

function InlineLoader({
  text = 'Loading...',
  size = 'sm',
}: InlineLoaderProps) {
  return (
    <span
      className="inline-flex items-center gap-1.5 text-gray-500"
      role="status"
      aria-label={text}
    >
      <Spinner size={size === 'sm' ? 'xs' : 'sm'} color="gray" />
      <span className={size === 'sm' ? 'text-caption' : 'text-body-sm'}>{text}</span>
    </span>
  )
}
```

#### Dot Loader Variant

```tsx
function DotLoader() {
  return (
    <span className="inline-flex items-center gap-1" role="status" aria-label="Loading">
      {[0, 1, 2].map((i) => (
        <span
          key={i}
          className="w-1.5 h-1.5 bg-gray-400 rounded-full animate-bounce"
          style={{ animationDelay: `${i * 0.15}s` }}
        />
      ))}
    </span>
  )
}
```

#### Usage Examples

```tsx
// Inline loading
<InlineLoader text="Fetching data..." />

// In a button
<button className="text-body-sm text-mereka-teal">
  <InlineLoader text="Saving" size="sm" />
</button>

// Dot loader for chat
<div className="flex items-center gap-2">
  <AvatarSkeleton size="sm" />
  <div className="bg-gray-100 rounded-lg px-3 py-2">
    <DotLoader />
  </div>
</div>
```

---

## 3. Progress Indicators

For operations with known duration or measurable progress.

### Upload Progress Bar

```tsx
interface UploadProgressProps {
  progress: number // 0-100
  fileName?: string
  fileSize?: string
  status?: 'uploading' | 'processing' | 'complete' | 'error'
  onCancel?: () => void
}

function UploadProgress({
  progress,
  fileName,
  fileSize,
  status = 'uploading',
  onCancel,
}: UploadProgressProps) {
  const statusColors = {
    uploading: 'bg-mereka-teal',
    processing: 'bg-mereka-blue',
    complete: 'bg-mereka-forest',
    error: 'bg-mereka-burgundy',
  }

  const statusIcons = {
    uploading: <UploadCloudIcon className="w-5 h-5 text-mereka-teal" />,
    processing: <Spinner size="sm" color="primary" />,
    complete: <CheckCircleIcon className="w-5 h-5 text-mereka-forest" />,
    error: <AlertCircleIcon className="w-5 h-5 text-mereka-burgundy" />,
  }

  const statusText = {
    uploading: `Uploading... ${progress}%`,
    processing: 'Processing...',
    complete: 'Upload complete',
    error: 'Upload failed',
  }

  return (
    <div className="bg-white rounded-lg border border-gray-200 p-4">
      <div className="flex items-start gap-3">
        {/* Icon */}
        <div className="shrink-0">{statusIcons[status]}</div>

        {/* Content */}
        <div className="flex-1 min-w-0">
          <div className="flex items-center justify-between gap-2">
            <p className="font-body text-body-sm font-medium text-gray-900 truncate">
              {fileName || 'File'}
            </p>
            {onCancel && status === 'uploading' && (
              <button
                onClick={onCancel}
                className="text-gray-400 hover:text-gray-600 transition-colors"
                aria-label="Cancel upload"
              >
                <XIcon className="w-4 h-4" />
              </button>
            )}
          </div>

          {/* Progress bar */}
          <div className="mt-2 h-2 bg-gray-200 rounded-full overflow-hidden">
            <div
              className={`h-full ${statusColors[status]} transition-all duration-300 ease-out`}
              style={{ width: `${status === 'complete' ? 100 : progress}%` }}
            />
          </div>

          {/* Status text */}
          <div className="flex items-center justify-between mt-1.5">
            <span className="text-caption text-gray-500">{statusText[status]}</span>
            {fileSize && (
              <span className="text-caption text-gray-400">{fileSize}</span>
            )}
          </div>
        </div>
      </div>
    </div>
  )
}
```

#### Usage Examples

```tsx
// Uploading state
<UploadProgress
  progress={45}
  fileName="pottery-workshop.jpg"
  fileSize="2.4 MB"
  status="uploading"
  onCancel={() => console.log('Cancelled')}
/>

// Processing state
<UploadProgress
  progress={100}
  fileName="pottery-workshop.jpg"
  status="processing"
/>

// Complete state
<UploadProgress
  progress={100}
  fileName="pottery-workshop.jpg"
  fileSize="2.4 MB"
  status="complete"
/>

// Error state
<UploadProgress
  progress={67}
  fileName="large-video.mp4"
  status="error"
/>
```

---

### Linear Progress Bar

Generic progress bar for any operation.

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const progressBarVariants = cva(
  'h-2 rounded-full overflow-hidden',
  {
    variants: {
      variant: {
        default: 'bg-gray-200',
        subtle: 'bg-gray-100',
      },
      size: {
        sm: 'h-1',
        md: 'h-2',
        lg: 'h-3',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'md',
    },
  }
)

interface ProgressBarProps extends VariantProps<typeof progressBarVariants> {
  progress: number
  color?: 'teal' | 'blue' | 'green' | 'orange'
  showLabel?: boolean
  animated?: boolean
}

function ProgressBar({
  progress,
  variant,
  size,
  color = 'teal',
  showLabel = false,
  animated = true,
}: ProgressBarProps) {
  const colorClasses = {
    teal: 'bg-mereka-teal',
    blue: 'bg-mereka-blue',
    green: 'bg-mereka-forest',
    orange: 'bg-mereka-orange',
  }

  return (
    <div className="space-y-1">
      {showLabel && (
        <div className="flex justify-between text-caption text-gray-500">
          <span>Progress</span>
          <span>{Math.round(progress)}%</span>
        </div>
      )}
      <div className={progressBarVariants({ variant, size })}>
        <div
          className={`h-full ${colorClasses[color]} ${
            animated ? 'transition-all duration-300 ease-out' : ''
          }`}
          style={{ width: `${Math.min(100, Math.max(0, progress))}%` }}
          role="progressbar"
          aria-valuenow={progress}
          aria-valuemin={0}
          aria-valuemax={100}
        />
      </div>
    </div>
  )
}
```

---

### Multi-Step Form Progress

Progress indicator for multi-step forms and wizards.

```tsx
interface Step {
  id: string
  label: string
  description?: string
}

interface StepProgressProps {
  steps: Step[]
  currentStep: number // 0-indexed
  variant?: 'horizontal' | 'vertical'
}

function StepProgress({
  steps,
  currentStep,
  variant = 'horizontal',
}: StepProgressProps) {
  if (variant === 'vertical') {
    return (
      <nav aria-label="Progress">
        <ol className="space-y-4">
          {steps.map((step, index) => {
            const isComplete = index < currentStep
            const isCurrent = index === currentStep
            const isPending = index > currentStep

            return (
              <li key={step.id} className="flex gap-4">
                {/* Step indicator */}
                <div className="flex flex-col items-center">
                  <div
                    className={`w-8 h-8 rounded-full flex items-center justify-center font-medium text-sm transition-colors ${
                      isComplete
                        ? 'bg-mereka-teal text-white'
                        : isCurrent
                        ? 'bg-mereka-teal text-white'
                        : 'bg-gray-200 text-gray-500'
                    }`}
                  >
                    {isComplete ? (
                      <CheckIcon className="w-4 h-4" />
                    ) : (
                      index + 1
                    )}
                  </div>
                  {index < steps.length - 1 && (
                    <div
                      className={`w-0.5 flex-1 min-h-[24px] mt-2 ${
                        isComplete ? 'bg-mereka-teal' : 'bg-gray-200'
                      }`}
                    />
                  )}
                </div>

                {/* Step content */}
                <div className="pb-4">
                  <p
                    className={`font-medium ${
                      isCurrent ? 'text-mereka-teal' : isPending ? 'text-gray-400' : 'text-gray-900'
                    }`}
                  >
                    {step.label}
                  </p>
                  {step.description && (
                    <p className="text-body-sm text-gray-500 mt-0.5">
                      {step.description}
                    </p>
                  )}
                </div>
              </li>
            )
          })}
        </ol>
      </nav>
    )
  }

  // Horizontal variant
  return (
    <nav aria-label="Progress" className="w-full">
      <ol className="flex items-center">
        {steps.map((step, index) => {
          const isComplete = index < currentStep
          const isCurrent = index === currentStep
          const isPending = index > currentStep

          return (
            <li
              key={step.id}
              className={`flex items-center ${
                index < steps.length - 1 ? 'flex-1' : ''
              }`}
            >
              {/* Step indicator */}
              <div className="flex flex-col items-center">
                <div
                  className={`w-10 h-10 rounded-full flex items-center justify-center font-medium transition-colors ${
                    isComplete
                      ? 'bg-mereka-teal text-white'
                      : isCurrent
                      ? 'bg-mereka-teal text-white'
                      : 'bg-gray-200 text-gray-500'
                  }`}
                >
                  {isComplete ? (
                    <CheckIcon className="w-5 h-5" />
                  ) : (
                    index + 1
                  )}
                </div>
                <span
                  className={`mt-2 text-caption font-medium whitespace-nowrap ${
                    isCurrent ? 'text-mereka-teal' : isPending ? 'text-gray-400' : 'text-gray-600'
                  }`}
                >
                  {step.label}
                </span>
              </div>

              {/* Connector line */}
              {index < steps.length - 1 && (
                <div className="flex-1 mx-4">
                  <div
                    className={`h-0.5 ${
                      isComplete ? 'bg-mereka-teal' : 'bg-gray-200'
                    }`}
                  />
                </div>
              )}
            </li>
          )
        })}
      </ol>
    </nav>
  )
}
```

#### Usage Examples

```tsx
// Horizontal step progress
<StepProgress
  steps={[
    { id: 'details', label: 'Details' },
    { id: 'schedule', label: 'Schedule' },
    { id: 'pricing', label: 'Pricing' },
    { id: 'review', label: 'Review' },
  ]}
  currentStep={1}
/>

// Vertical with descriptions
<StepProgress
  variant="vertical"
  steps={[
    {
      id: 'account',
      label: 'Create Account',
      description: 'Enter your email and create a password',
    },
    {
      id: 'profile',
      label: 'Complete Profile',
      description: 'Tell us about yourself',
    },
    {
      id: 'preferences',
      label: 'Set Preferences',
      description: 'Choose your interests',
    },
  ]}
  currentStep={1}
/>
```

---

## 4. Shimmer Animation Effect

Shimmer adds a sweeping highlight effect to skeleton loaders for a polished appearance.

### Shimmer CSS

```css
/* Add to your global CSS */
@keyframes shimmer {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(100%);
  }
}

.animate-shimmer {
  animation: shimmer 1.5s infinite;
}

/* Shimmer overlay class */
.shimmer-overlay {
  position: relative;
  overflow: hidden;
}

.shimmer-overlay::before {
  content: '';
  position: absolute;
  inset: 0;
  transform: translateX(-100%);
  background: linear-gradient(
    90deg,
    transparent,
    rgba(255, 255, 255, 0.6),
    transparent
  );
  animation: shimmer 1.5s infinite;
}
```

### Shimmer Wrapper Component

```tsx
interface ShimmerWrapperProps {
  children: React.ReactNode
  isLoading?: boolean
}

function ShimmerWrapper({ children, isLoading = true }: ShimmerWrapperProps) {
  if (!isLoading) return <>{children}</>

  return (
    <div className="relative overflow-hidden">
      {children}
      <div
        className="absolute inset-0 -translate-x-full animate-[shimmer_1.5s_infinite] bg-gradient-to-r from-transparent via-white/60 to-transparent"
        aria-hidden="true"
      />
    </div>
  )
}
```

#### Usage Examples

```tsx
// Wrap any skeleton
<ShimmerWrapper isLoading={isLoading}>
  <div className="h-40 bg-gray-200 rounded-xl" />
</ShimmerWrapper>

// With skeleton component
<Skeleton animation="shimmer" className="h-6 w-3/4" />

// Card with shimmer
<ExperienceCardSkeleton animation="shimmer" />
```

---

## Loading State Patterns

### Conditional Loading Pattern

```tsx
interface AsyncContentProps<T> {
  isLoading: boolean
  error?: Error | null
  data: T | null
  skeleton: React.ReactNode
  children: (data: T) => React.ReactNode
}

function AsyncContent<T>({
  isLoading,
  error,
  data,
  skeleton,
  children,
}: AsyncContentProps<T>) {
  if (error) {
    return (
      <div className="text-center py-8">
        <AlertCircleIcon className="w-12 h-12 text-mereka-burgundy mx-auto mb-3" />
        <p className="text-body text-gray-600">Something went wrong</p>
        <p className="text-body-sm text-gray-400 mt-1">{error.message}</p>
      </div>
    )
  }

  if (isLoading || !data) {
    return <>{skeleton}</>
  }

  return <>{children(data)}</>
}
```

#### Usage Example

```tsx
<AsyncContent
  isLoading={isLoading}
  error={error}
  data={experiences}
  skeleton={
    <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
      {Array.from({ length: 6 }).map((_, i) => (
        <ExperienceCardSkeleton key={i} animation="shimmer" />
      ))}
    </div>
  }
>
  {(experiences) => (
    <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
      {experiences.map((exp) => (
        <ExperienceCard key={exp.id} {...exp} />
      ))}
    </div>
  )}
</AsyncContent>
```

### Optimistic Loading Pattern

```tsx
function useOptimisticUpdate<T>(
  initialData: T,
  updateFn: (data: T) => Promise<T>
) {
  const [data, setData] = useState(initialData)
  const [isUpdating, setIsUpdating] = useState(false)
  const [error, setError] = useState<Error | null>(null)

  const update = async (optimisticData: T) => {
    const previousData = data
    setData(optimisticData) // Optimistic update
    setIsUpdating(true)
    setError(null)

    try {
      const result = await updateFn(optimisticData)
      setData(result)
    } catch (err) {
      setData(previousData) // Rollback on error
      setError(err as Error)
    } finally {
      setIsUpdating(false)
    }
  }

  return { data, isUpdating, error, update }
}
```

---

## Accessibility

### Screen Reader Announcements

```tsx
// Announce loading state changes
function LoadingAnnouncer({ isLoading, message }: { isLoading: boolean; message: string }) {
  return (
    <div
      role="status"
      aria-live="polite"
      aria-atomic="true"
      className="sr-only"
    >
      {isLoading ? message : ''}
    </div>
  )
}

// Usage
<LoadingAnnouncer isLoading={isLoading} message="Loading experiences..." />
```

### Focus Management

```tsx
// Return focus after loading completes
function useFocusReturn(isLoading: boolean, targetRef: React.RefObject<HTMLElement>) {
  const previousFocusRef = useRef<HTMLElement | null>(null)

  useEffect(() => {
    if (isLoading) {
      previousFocusRef.current = document.activeElement as HTMLElement
    } else if (previousFocusRef.current) {
      // Option 1: Return to previous focus
      previousFocusRef.current.focus()
      // Option 2: Focus on new content
      // targetRef.current?.focus()
    }
  }, [isLoading])
}
```

### Reduced Motion

```tsx
// Respect user's motion preferences
const shimmerClass = `
  @media (prefers-reduced-motion: no-preference) {
    animation: shimmer 1.5s infinite;
  }
  @media (prefers-reduced-motion: reduce) {
    animation: none;
  }
`

// Component that respects motion preferences
function AccessibleSkeleton({ className }: { className?: string }) {
  return (
    <div
      className={`bg-gray-200 rounded motion-safe:animate-pulse ${className}`}
    />
  )
}
```

---

## Do's and Don'ts

### Do's

- **Do** match skeleton shapes to actual content dimensions
- **Do** use shimmer animation for polished appearance
- **Do** show skeletons immediately while content loads
- **Do** include appropriate ARIA attributes for screen readers
- **Do** use progress indicators when duration is known
- **Do** provide cancel options for long operations
- **Do** show descriptive loading messages when helpful
- **Do** respect user's reduced motion preferences
- **Do** maintain consistent loading patterns across the app

### Don'ts

- **Don't** show spinners for content with known shapes
- **Don't** use multiple different loading styles on one page
- **Don't** block the entire UI for partial updates
- **Don't** show loading states for instant operations
- **Don't** forget to handle error states
- **Don't** use vague messages like "Please wait..."
- **Don't** make skeletons flash by hiding them too quickly
- **Don't** skip loading states assuming fast connections

---

## Performance Tips

### Minimum Display Time

Prevent loading flicker by ensuring minimum display time:

```tsx
function useMinimumLoadingTime(isLoading: boolean, minTime = 300) {
  const [showLoading, setShowLoading] = useState(isLoading)
  const startTimeRef = useRef<number | null>(null)

  useEffect(() => {
    if (isLoading) {
      startTimeRef.current = Date.now()
      setShowLoading(true)
    } else if (startTimeRef.current) {
      const elapsed = Date.now() - startTimeRef.current
      const remaining = Math.max(0, minTime - elapsed)

      setTimeout(() => {
        setShowLoading(false)
        startTimeRef.current = null
      }, remaining)
    }
  }, [isLoading, minTime])

  return showLoading
}
```

### Lazy Loading Skeletons

```tsx
// Only render skeletons when in viewport
import { useInView } from 'react-intersection-observer'

function LazySkeletonGrid({ count }: { count: number }) {
  const { ref, inView } = useInView({
    triggerOnce: true,
    rootMargin: '100px',
  })

  return (
    <div ref={ref} className="grid grid-cols-1 md:grid-cols-3 gap-6">
      {inView &&
        Array.from({ length: count }).map((_, i) => (
          <ExperienceCardSkeleton key={i} animation="shimmer" />
        ))}
    </div>
  )
}
```

---

## Related Documentation

- [Components](./components.md) - UI components that use loading states
- [Cards](./cards.md) - Card components and their skeleton variants
- [Buttons](./buttons.md) - Button loading states
- [Inputs](./inputs.md) - Form input states during submission
- [Patterns](./patterns.md) - Common loading patterns in the app
