# Components

> Overview of all UI components in the Mereka design system.

**Figma Sections**: Visuals, Indicators, Cards, Inputs, Dialogs

---

## Component Inventory

| Category | Components |
|----------|------------|
| **Avatars** | User, Hub, Guest, Placeholder |
| **Tags** | Standard, Info, Positive, Warning, Negative, Neutral |
| **Badges** | Notification, Status indicators |
| **Cards** | Experience, Hub, Expert, Collection, Review |
| **Inputs** | Text, Select, Date picker, Time picker, File upload |
| **Buttons** | [See buttons.md](./buttons.md) |
| **Navigation** | [See navigation.md](./navigation.md) |

---

## Avatars

Avatars represent users, hubs, and other entities.

### Types

| Type | Usage |
|------|-------|
| **User** | Individual learners and experts |
| **Hub** | Organizations (shows logo or initial) |
| **Guest** | Anonymous or unregistered users |
| **Placeholder** | No image available |

### Sizes

| Size | Dimensions | Usage |
|------|------------|-------|
| `xs` | 24px | Inline mentions, compact lists |
| `sm` | 36px | Comments, small cards |
| `md` | 42px | List items, navigation |
| `lg` | 64px | Profile cards, medium emphasis |
| `xl` | 72px | Profile headers |
| `2xl` | 96px | Large profile displays |

### Implementation

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const avatarVariants = cva(
  'relative rounded-full overflow-hidden bg-gray-200 flex items-center justify-center',
  {
    variants: {
      size: {
        xs: 'w-6 h-6 text-xs',
        sm: 'w-9 h-9 text-sm',
        md: 'w-[42px] h-[42px] text-base',
        lg: 'w-16 h-16 text-lg',
        xl: 'w-[72px] h-[72px] text-xl',
        '2xl': 'w-24 h-24 text-2xl',
      },
    },
    defaultVariants: {
      size: 'md',
    },
  }
)

interface AvatarProps extends VariantProps<typeof avatarVariants> {
  src?: string
  alt: string
  fallback?: string
}

export function Avatar({ src, alt, fallback, size }: AvatarProps) {
  return (
    <div className={avatarVariants({ size })}>
      {src ? (
        <img src={src} alt={alt} className="w-full h-full object-cover" />
      ) : (
        <span className="font-medium text-gray-600">
          {fallback || alt.charAt(0).toUpperCase()}
        </span>
      )}
    </div>
  )
}
```

### Usage

```tsx
// User with image
<Avatar src="/user.jpg" alt="John Doe" size="lg" />

// Hub with fallback
<Avatar alt="Mereka Hub" fallback="M" size="xl" />

// Guest placeholder
<Avatar alt="Guest" size="sm" />
```

---

## Tags

Tags categorize and label content.

### Variants

| Variant | Background | Text | Usage |
|---------|------------|------|-------|
| **Standard** | `gray-100` | `gray-700` | Default categorization |
| **Info** | `blue-100` | `blue-700` | Informational |
| **Positive** | `green-100` | `green-700` | Success, confirmed |
| **Warning** | `yellow-100` | `yellow-700` | Caution, pending |
| **Negative** | `red-100` | `red-700` | Error, cancelled |
| **Neutral** | `gray-50` | `gray-500` | Disabled, inactive |
| **Label** | `transparent` | `gray-600` | Plain text label |
| **Optional** | `gray-50` border | `gray-400` | Optional fields |

### Implementation

```tsx
const tagVariants = cva(
  'inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium',
  {
    variants: {
      variant: {
        standard: 'bg-gray-100 text-gray-700',
        info: 'bg-blue-100 text-blue-700',
        positive: 'bg-green-100 text-green-700',
        warning: 'bg-yellow-100 text-yellow-700',
        negative: 'bg-red-100 text-red-700',
        neutral: 'bg-gray-50 text-gray-500',
        label: 'text-gray-600',
        optional: 'border border-gray-200 text-gray-400',
      },
    },
    defaultVariants: {
      variant: 'standard',
    },
  }
)

export function Tag({ children, variant }: TagProps) {
  return <span className={tagVariants({ variant })}>{children}</span>
}
```

### Usage Examples

```tsx
// Experience category
<Tag variant="standard">Workshop</Tag>

// Booking status
<Tag variant="positive">Confirmed</Tag>
<Tag variant="warning">Pending</Tag>
<Tag variant="negative">Cancelled</Tag>

// Price tag
<Tag variant="info">RM 150</Tag>
```

---

## Badges

Small indicators for notifications and status.

### Notification Badge

```tsx
// Dot indicator (unread)
<div className="relative">
  <BellIcon />
  <span className="absolute -top-1 -right-1 w-2 h-2 bg-red-500 rounded-full" />
</div>

// Count badge
<div className="relative">
  <BellIcon />
  <span className="absolute -top-2 -right-2 w-5 h-5 bg-red-500 text-white text-xs rounded-full flex items-center justify-center">
    3
  </span>
</div>
```

### Status Indicators

```tsx
// Success
<span className="w-2 h-2 bg-green-500 rounded-full" />

// Warning
<span className="w-2 h-2 bg-yellow-500 rounded-full" />

// Error
<span className="w-2 h-2 bg-red-500 rounded-full" />

// Info
<span className="w-2 h-2 bg-blue-500 rounded-full" />
```

---

## Cards

Cards contain related content and actions.

### Card Types

| Type | Usage | Key Elements |
|------|-------|--------------|
| **Experience Card** | Browse experiences | Image, title, price, date, host |
| **Hub Card** | Browse organizations | Logo, name, location, rating |
| **Expert Card** | Expert profiles | Avatar, name, expertise, rate |
| **Collection Card** | Curated lists | Image, title, count |
| **Review Card** | User reviews | Avatar, rating, text, date |

### Base Card Structure

```tsx
<article className="bg-white rounded-xl shadow-md overflow-hidden">
  {/* Image */}
  <div className="aspect-video relative">
    <img src={image} alt={title} className="w-full h-full object-cover" />
  </div>

  {/* Content */}
  <div className="p-4">
    <h3 className="font-heading text-h4 text-gray-900">{title}</h3>
    <p className="font-body text-body-sm text-gray-600 mt-1">{description}</p>

    {/* Meta */}
    <div className="flex items-center gap-2 mt-3 text-caption text-gray-400">
      <span>{date}</span>
      <span>•</span>
      <span>{location}</span>
    </div>

    {/* Price/Action */}
    <div className="flex items-center justify-between mt-4">
      <span className="font-heading text-h4 text-gray-900">{price}</span>
      <Button size="default">Book</Button>
    </div>
  </div>
</article>
```

### Experience Card Example

```tsx
<ExperienceCard
  image="/workshop.jpg"
  title="Introduction to Pottery"
  description="Learn wheel throwing basics in this hands-on workshop"
  date="Sat, Jan 20"
  location="KL"
  price="RM 150"
  host={{
    name: "Clay Studio",
    avatar: "/studio.jpg"
  }}
/>
```

---

## Inputs

See [inputs.md](./inputs.md) for detailed input documentation.

### Quick Reference

| Input Type | Usage |
|------------|-------|
| Text | Names, emails, descriptions |
| Select | Dropdowns, category selection |
| Date Picker | Booking dates, schedules |
| Time Picker | Session times, durations |
| File Upload | Cover photos, galleries |
| Checkbox | Multi-select options |
| Radio | Single-select options |
| Toggle | On/off settings |

---

## Tooltips

Contextual information on hover.

```tsx
<Tooltip content="Save this experience for later">
  <button>
    <HeartIcon />
  </button>
</Tooltip>
```

### Positions

- **Top** (default): Appears above the trigger
- **Bottom**: Appears below
- **Left/Right**: For edge cases

---

## Dialogs

Modal windows for focused interactions.

### Types

| Type | Usage |
|------|-------|
| **Alert** | Confirmations, warnings |
| **Form** | Data entry, multi-step |
| **Content** | Detail views, previews |

### Alert Dialog

```tsx
<AlertDialog>
  <AlertDialog.Content className="max-w-md">
    <AlertDialog.Title>Cancel Booking?</AlertDialog.Title>
    <AlertDialog.Description>
      This action cannot be undone. Your payment will be refunded within 5-7 business days.
    </AlertDialog.Description>
    <div className="flex gap-3 mt-6">
      <AlertDialog.Cancel asChild>
        <Button variant="outline">Keep Booking</Button>
      </AlertDialog.Cancel>
      <AlertDialog.Action asChild>
        <Button variant="solid">Cancel Booking</Button>
      </AlertDialog.Action>
    </div>
  </AlertDialog.Content>
</AlertDialog>
```

---

## Rating

Star-based ratings display.

```tsx
// Display rating
<div className="flex items-center gap-1">
  {[1, 2, 3, 4, 5].map((star) => (
    <StarIcon
      key={star}
      className={star <= rating ? 'text-yellow-400 fill-yellow-400' : 'text-gray-300'}
    />
  ))}
  <span className="ml-2 text-body-sm text-gray-600">
    {rating} ({reviewCount} reviews)
  </span>
</div>
```

---

## Dividers

Visual separation between content.

```tsx
// Horizontal
<hr className="border-t border-gray-200 my-6" />

// Vertical (in flex container)
<div className="w-px bg-gray-200 self-stretch" />

// With text
<div className="flex items-center gap-4">
  <hr className="flex-1 border-gray-200" />
  <span className="text-caption text-gray-400">or</span>
  <hr className="flex-1 border-gray-200" />
</div>
```
