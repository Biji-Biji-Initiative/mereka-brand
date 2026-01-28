# UI Patterns

> Common patterns for building Mereka interfaces, mapped to backend domain models.

---

## Domain-to-UI Mapping

Mereka's backend has 50+ data models. Here's how to display them:

| Domain Entity | Primary UI | Secondary UI |
|---------------|------------|--------------|
| **Experience** | Experience Card, Detail Page | Calendar, Booking Form |
| **Hub** | Hub Card, Hub Profile | Team List, Experience Grid |
| **Expert** | Expert Card, Profile | Expertise List, Availability |
| **Booking** | Booking Card, Confirmation | Status Badge, Receipt |
| **Review** | Review Card, Rating Display | Review Form |
| **User** | Avatar, Profile Header | Settings Form |

---

## Experience Patterns

An **Experience** is a workshop, event, or learning session.

### Experience Card (Collection View)

```tsx
interface Experience {
  id: string
  title: string
  description: string
  coverImage: string
  price: number
  currency: string
  date: Date
  location: {
    type: 'physical' | 'virtual' | 'hybrid'
    address?: string
  }
  host: {
    name: string
    avatar: string
  }
  rating?: number
  reviewCount?: number
}

function ExperienceCard({ experience }: { experience: Experience }) {
  return (
    <article className="bg-white rounded-xl shadow-md overflow-hidden group">
      {/* Image */}
      <div className="aspect-video relative overflow-hidden">
        <img
          src={experience.coverImage}
          alt={experience.title}
          className="w-full h-full object-cover group-hover:scale-105 transition-transform"
        />
        {/* Location badge */}
        <span className="absolute top-3 left-3 bg-white/90 rounded-full px-2 py-1 text-caption">
          {experience.location.type === 'virtual' ? 'Online' : experience.location.address}
        </span>
      </div>

      <div className="p-4">
        {/* Host */}
        <div className="flex items-center gap-2 mb-2">
          <Avatar src={experience.host.avatar} alt={experience.host.name} size="xs" />
          <span className="text-caption text-gray-600">{experience.host.name}</span>
        </div>

        {/* Title */}
        <h3 className="font-heading text-h4 text-gray-900 line-clamp-2">
          {experience.title}
        </h3>

        {/* Rating */}
        {experience.rating && (
          <div className="flex items-center gap-1 mt-2">
            <StarIcon className="w-4 h-4 text-yellow-400 fill-yellow-400" />
            <span className="text-body-sm">{experience.rating}</span>
            <span className="text-caption text-gray-400">({experience.reviewCount})</span>
          </div>
        )}

        {/* Footer */}
        <div className="flex items-center justify-between mt-4 pt-4 border-t border-gray-100">
          <div>
            <span className="text-caption text-gray-400">From</span>
            <p className="font-heading text-h4 text-gray-900">
              {experience.currency} {experience.price}
            </p>
          </div>
          <span className="text-body-sm text-gray-600">
            {formatDate(experience.date)}
          </span>
        </div>
      </div>
    </article>
  )
}
```

---

## Hub Patterns

A **Hub** is an organization offering experiences.

### Hub Card

```tsx
interface Hub {
  id: string
  name: string
  logo: string
  description: string
  location: string
  experienceCount: number
  rating?: number
}

function HubCard({ hub }: { hub: Hub }) {
  return (
    <article className="bg-white rounded-xl shadow-md p-6 flex gap-4">
      <Avatar src={hub.logo} alt={hub.name} size="xl" />
      <div className="flex-1">
        <h3 className="font-heading text-h3 text-gray-900">{hub.name}</h3>
        <p className="text-body-sm text-gray-600 mt-1 line-clamp-2">
          {hub.description}
        </p>
        <div className="flex items-center gap-4 mt-3 text-caption text-gray-400">
          <span className="flex items-center gap-1">
            <LocationIcon className="w-4 h-4" />
            {hub.location}
          </span>
          <span>{hub.experienceCount} experiences</span>
        </div>
      </div>
    </article>
  )
}
```

---

## Booking Patterns

A **Booking** represents a user's reservation.

### Booking Status

| Status | Tag Variant | Badge Color |
|--------|-------------|-------------|
| `pending` | warning | Yellow |
| `confirmed` | positive | Green |
| `cancelled` | negative | Red |
| `completed` | neutral | Gray |

```tsx
function BookingStatusBadge({ status }: { status: BookingStatus }) {
  const variants = {
    pending: 'warning',
    confirmed: 'positive',
    cancelled: 'negative',
    completed: 'neutral',
  }

  const labels = {
    pending: 'Pending',
    confirmed: 'Confirmed',
    cancelled: 'Cancelled',
    completed: 'Completed',
  }

  return <Tag variant={variants[status]}>{labels[status]}</Tag>
}
```

### Booking Card

```tsx
function BookingCard({ booking }: { booking: Booking }) {
  return (
    <article className="bg-white rounded-xl shadow-sm border border-gray-200 p-4">
      <div className="flex gap-4">
        <img
          src={booking.experience.coverImage}
          alt={booking.experience.title}
          className="w-24 h-24 rounded-lg object-cover"
        />
        <div className="flex-1">
          <div className="flex items-start justify-between">
            <h3 className="font-heading text-h4">{booking.experience.title}</h3>
            <BookingStatusBadge status={booking.status} />
          </div>
          <p className="text-body-sm text-gray-600 mt-1">
            {formatDate(booking.date)} at {formatTime(booking.time)}
          </p>
          <p className="text-body-sm text-gray-400 mt-1">
            {booking.experience.host.name}
          </p>
        </div>
      </div>

      <div className="flex items-center justify-between mt-4 pt-4 border-t border-gray-100">
        <span className="font-heading text-h4">
          {booking.currency} {booking.total}
        </span>
        <Button variant="outline" size="default">View Details</Button>
      </div>
    </article>
  )
}
```

---

## Form Patterns

### Multi-Step Form

Used for experience creation, booking, onboarding.

```tsx
function MultiStepForm({ steps, currentStep, children }) {
  return (
    <div className="min-h-screen flex flex-col">
      {/* Progress */}
      <header className="border-b border-gray-200 py-4">
        <div className="container mx-auto px-4">
          <div className="flex items-center gap-2">
            {steps.map((step, index) => (
              <React.Fragment key={step.id}>
                <div className={`
                  w-8 h-8 rounded-full flex items-center justify-center text-sm font-medium
                  ${index < currentStep ? 'bg-mereka-teal text-white' : ''}
                  ${index === currentStep ? 'bg-gray-900 text-white' : ''}
                  ${index > currentStep ? 'bg-gray-200 text-gray-400' : ''}
                `}>
                  {index < currentStep ? <CheckIcon /> : index + 1}
                </div>
                {index < steps.length - 1 && (
                  <div className={`flex-1 h-0.5 ${index < currentStep ? 'bg-mereka-teal' : 'bg-gray-200'}`} />
                )}
              </React.Fragment>
            ))}
          </div>
        </div>
      </header>

      {/* Content */}
      <main className="flex-1 py-8">
        <div className="container mx-auto px-4 max-w-2xl">
          <h1 className="font-heading text-h1 mb-2">{steps[currentStep].title}</h1>
          <p className="text-body text-gray-600 mb-8">{steps[currentStep].description}</p>
          {children}
        </div>
      </main>

      {/* Footer */}
      <footer className="border-t border-gray-200 py-4">
        <div className="container mx-auto px-4 flex justify-between">
          <Button variant="ghost" disabled={currentStep === 0}>Back</Button>
          <Button variant="solid">
            {currentStep === steps.length - 1 ? 'Submit' : 'Continue'}
          </Button>
        </div>
      </footer>
    </div>
  )
}
```

---

## Empty States

When there's no content to display.

```tsx
function EmptyState({ type }: { type: 'hub' | 'learner' | 'general' }) {
  const content = {
    hub: {
      title: 'No experiences yet',
      description: 'Create your first experience to start welcoming learners.',
      action: 'Create Experience',
    },
    learner: {
      title: 'Nothing here yet',
      description: 'Explore experiences and start your learning journey.',
      action: 'Browse Experiences',
    },
    general: {
      title: 'No results found',
      description: 'Try adjusting your search or filters.',
      action: 'Clear Filters',
    },
  }

  return (
    <div className="text-center py-16">
      <EmptyStateIllustration type={type} className="mx-auto w-48 h-48 mb-6" />
      <h2 className="font-heading text-h2 text-gray-900">{content[type].title}</h2>
      <p className="text-body text-gray-600 mt-2 max-w-md mx-auto">
        {content[type].description}
      </p>
      <Button variant="solid" className="mt-6">
        {content[type].action}
      </Button>
    </div>
  )
}
```

---

## Loading States

### Skeleton Cards

```tsx
function ExperienceCardSkeleton() {
  return (
    <div className="bg-white rounded-xl shadow-md overflow-hidden animate-pulse">
      <div className="aspect-video bg-gray-200" />
      <div className="p-4 space-y-3">
        <div className="flex items-center gap-2">
          <div className="w-6 h-6 rounded-full bg-gray-200" />
          <div className="h-3 w-24 bg-gray-200 rounded" />
        </div>
        <div className="h-5 w-full bg-gray-200 rounded" />
        <div className="h-4 w-3/4 bg-gray-200 rounded" />
        <div className="flex justify-between pt-4 border-t border-gray-100">
          <div className="h-6 w-20 bg-gray-200 rounded" />
          <div className="h-4 w-16 bg-gray-200 rounded" />
        </div>
      </div>
    </div>
  )
}
```

---

## Notification Patterns

Based on the 50+ email types in the backend:

### Notification Types

| Category | Examples | Icon | Color |
|----------|----------|------|-------|
| **Booking** | Confirmed, Reminder, Cancelled | Calendar | Blue |
| **Payment** | Success, Failed, Refund | CreditCard | Green/Red |
| **Hub** | Invitation, Approved, Member joined | Building | Teal |
| **Message** | New message, Unread digest | Mail | Gray |
| **System** | Maintenance, Updates | Info | Orange |

```tsx
function NotificationItem({ notification }) {
  const icons = {
    booking: CalendarIcon,
    payment: CreditCardIcon,
    hub: BuildingIcon,
    message: MailIcon,
    system: InfoIcon,
  }

  const Icon = icons[notification.category]

  return (
    <div className={`flex gap-3 p-4 ${notification.read ? '' : 'bg-gray-50'}`}>
      <div className="w-10 h-10 rounded-full bg-gray-100 flex items-center justify-center">
        <Icon className="w-5 h-5 text-gray-600" />
      </div>
      <div className="flex-1">
        <p className="text-body">{notification.title}</p>
        <p className="text-body-sm text-gray-600">{notification.message}</p>
        <p className="text-caption text-gray-400 mt-1">
          {formatRelativeTime(notification.createdAt)}
        </p>
      </div>
      {!notification.read && (
        <div className="w-2 h-2 bg-mereka-teal rounded-full" />
      )}
    </div>
  )
}
```

---

## Error Patterns

### Form Validation

```tsx
<div className="space-y-1">
  <Input
    label="Email"
    error={errors.email}
    className={errors.email ? 'border-red-500' : ''}
  />
  {errors.email && (
    <p className="text-caption text-red-600 flex items-center gap-1">
      <AlertCircleIcon className="w-3 h-3" />
      {errors.email}
    </p>
  )}
</div>
```

### Toast Notifications

```tsx
// Success
<Toast variant="success">
  <CheckCircleIcon />
  Booking confirmed!
</Toast>

// Error
<Toast variant="error">
  <XCircleIcon />
  Payment failed. Please try again.
</Toast>

// Warning
<Toast variant="warning">
  <AlertTriangleIcon />
  Your session is about to expire.
</Toast>
```
