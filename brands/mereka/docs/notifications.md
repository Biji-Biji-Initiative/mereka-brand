# Notifications

> Comprehensive notification system for the Mereka platform, covering 50+ notification types across toast messages, notification center, push notifications, and email previews.

**Figma Sections**: Feedback, Indicators, Dialogs

---

## Notification Architecture

```
Notification System
├── Toast Notifications       ← Immediate feedback (success, error, warning, info)
├── Notification Center       ← Inbox UI with read/unread states
├── Notification Items        ← Category-specific displays
├── Notification Badges       ← Counts and indicators
├── Push Notifications        ← Mobile/browser push patterns
└── Email Preview Cards       ← In-app email notification previews
```

---

## Notification Categories

Mereka's backend generates 50+ notification types, organized into these categories:

| Category | Types | Icon | Primary Color | Background |
|----------|-------|------|---------------|------------|
| **Booking** | Confirmed, Reminder, Cancelled, Rescheduled | `Calendar` | `mereka-blue` | `blue-50` |
| **Payment** | Success, Failed, Refund, Payout | `CreditCard` | `mereka-forest` / `mereka-burgundy` | `green-50` / `red-50` |
| **Hub** | Invitation, Approved, Member Joined, Role Changed | `Building2` | `mereka-teal` | `teal-50` |
| **Messages** | New Message, Unread Digest, Mention | `MessageSquare` | `gray-600` | `gray-50` |
| **Experience** | Approved, Rejected, Expired, Featured | `Sparkles` | `mereka-magenta` | `pink-50` |
| **Jobs** | Proposal Received, Offer Sent, Work Submitted, Completed | `Briefcase` | `mereka-blue` | `blue-50` |
| **System** | Maintenance, Updates, Security Alert | `Info` | `mereka-orange` | `orange-50` |

---

## 1. Toast Notifications

Toast notifications provide immediate feedback for user actions. They appear temporarily and auto-dismiss.

### Toast Variants

| Variant | Usage | Icon | Colors |
|---------|-------|------|--------|
| **Success** | Confirmations, completed actions | `CheckCircle` | Green background, forest text |
| **Error** | Failures, validation errors | `XCircle` | Red background, burgundy text |
| **Warning** | Cautions, pending issues | `AlertTriangle` | Yellow background, orange text |
| **Info** | Informational, neutral updates | `Info` | Blue background, blue text |

### Toast Component

```tsx
import { cva, type VariantProps } from 'class-variance-authority'
import {
  CheckCircle,
  XCircle,
  AlertTriangle,
  Info,
  X,
  type LucideIcon,
} from 'lucide-react'

const toastVariants = cva(
  'flex items-start gap-3 p-4 rounded-xl shadow-lg max-w-md w-full animate-in slide-in-from-top-2 fade-in duration-300',
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

const iconVariants = {
  success: { icon: CheckCircle, className: 'text-mereka-forest' },
  error: { icon: XCircle, className: 'text-mereka-burgundy' },
  warning: { icon: AlertTriangle, className: 'text-mereka-orange' },
  info: { icon: Info, className: 'text-mereka-blue' },
}

const textVariants = {
  success: 'text-mereka-forest',
  error: 'text-mereka-burgundy',
  warning: 'text-yellow-800',
  info: 'text-mereka-blue',
}

interface ToastProps extends VariantProps<typeof toastVariants> {
  title: string
  message?: string
  onDismiss?: () => void
  action?: {
    label: string
    onClick: () => void
  }
}

export function Toast({
  variant = 'info',
  title,
  message,
  onDismiss,
  action,
}: ToastProps) {
  const { icon: Icon, className: iconClass } = iconVariants[variant!]
  const textClass = textVariants[variant!]

  return (
    <div className={toastVariants({ variant })} role="alert">
      <Icon className={`w-5 h-5 flex-shrink-0 mt-0.5 ${iconClass}`} />

      <div className="flex-1 min-w-0">
        <p className={`font-medium text-body ${textClass}`}>{title}</p>
        {message && (
          <p className={`text-body-sm mt-1 ${textClass} opacity-80`}>
            {message}
          </p>
        )}
        {action && (
          <button
            onClick={action.onClick}
            className={`text-body-sm font-medium mt-2 underline hover:no-underline ${textClass}`}
          >
            {action.label}
          </button>
        )}
      </div>

      {onDismiss && (
        <button
          onClick={onDismiss}
          className={`p-1 rounded-full hover:bg-black/5 transition-colors ${textClass}`}
          aria-label="Dismiss"
        >
          <X className="w-4 h-4" />
        </button>
      )}
    </div>
  )
}
```

### Toast Usage Examples

```tsx
// Success toast
<Toast
  variant="success"
  title="Booking confirmed!"
  message="You'll receive a confirmation email shortly."
/>

// Error toast
<Toast
  variant="error"
  title="Payment failed"
  message="Your card was declined. Please try another payment method."
  action={{
    label: 'Try again',
    onClick: () => openPaymentModal(),
  }}
/>

// Warning toast
<Toast
  variant="warning"
  title="Session expiring soon"
  message="Your booking session will expire in 5 minutes."
  action={{
    label: 'Extend session',
    onClick: () => extendSession(),
  }}
/>

// Info toast
<Toast
  variant="info"
  title="New message"
  message="You have 3 unread messages from Clay Studio."
/>
```

### Toast Container

```tsx
interface ToastContainerProps {
  toasts: ToastProps[]
  position?: 'top-right' | 'top-center' | 'bottom-right' | 'bottom-center'
}

export function ToastContainer({
  toasts,
  position = 'top-right',
}: ToastContainerProps) {
  const positionClasses = {
    'top-right': 'top-4 right-4',
    'top-center': 'top-4 left-1/2 -translate-x-1/2',
    'bottom-right': 'bottom-4 right-4',
    'bottom-center': 'bottom-4 left-1/2 -translate-x-1/2',
  }

  return (
    <div
      className={`fixed z-50 flex flex-col gap-3 ${positionClasses[position]}`}
      aria-live="polite"
    >
      {toasts.map((toast, index) => (
        <Toast key={index} {...toast} />
      ))}
    </div>
  )
}
```

### Toast with Progress Bar

For toasts that auto-dismiss, show remaining time:

```tsx
interface TimedToastProps extends ToastProps {
  duration?: number // milliseconds, default 5000
}

export function TimedToast({
  duration = 5000,
  ...props
}: TimedToastProps) {
  const [progress, setProgress] = useState(100)

  useEffect(() => {
    const interval = setInterval(() => {
      setProgress((prev) => Math.max(0, prev - (100 / (duration / 100))))
    }, 100)

    const timeout = setTimeout(() => {
      props.onDismiss?.()
    }, duration)

    return () => {
      clearInterval(interval)
      clearTimeout(timeout)
    }
  }, [duration])

  const progressColors = {
    success: 'bg-mereka-forest',
    error: 'bg-mereka-burgundy',
    warning: 'bg-mereka-orange',
    info: 'bg-mereka-blue',
  }

  return (
    <div className="relative">
      <Toast {...props} />
      <div className="absolute bottom-0 left-0 right-0 h-1 bg-black/10 rounded-b-xl overflow-hidden">
        <div
          className={`h-full transition-all duration-100 ${progressColors[props.variant || 'info']}`}
          style={{ width: `${progress}%` }}
        />
      </div>
    </div>
  )
}
```

---

## 2. Notification Center / Dropdown

The notification center is an inbox-style dropdown showing all notifications.

### Notification Center Component

```tsx
import {
  Bell,
  Calendar,
  CreditCard,
  Building2,
  MessageSquare,
  Sparkles,
  Briefcase,
  Info,
  Check,
  Settings,
  type LucideIcon,
} from 'lucide-react'

type NotificationCategory =
  | 'booking'
  | 'payment'
  | 'hub'
  | 'message'
  | 'experience'
  | 'jobs'
  | 'system'

interface Notification {
  id: string
  category: NotificationCategory
  title: string
  message: string
  timestamp: Date
  read: boolean
  actionUrl?: string
  image?: string
  metadata?: Record<string, any>
}

const categoryConfig: Record<NotificationCategory, {
  icon: LucideIcon
  bgColor: string
  iconColor: string
}> = {
  booking: {
    icon: Calendar,
    bgColor: 'bg-blue-100',
    iconColor: 'text-mereka-blue',
  },
  payment: {
    icon: CreditCard,
    bgColor: 'bg-green-100',
    iconColor: 'text-mereka-forest',
  },
  hub: {
    icon: Building2,
    bgColor: 'bg-mereka-teal/10',
    iconColor: 'text-mereka-teal',
  },
  message: {
    icon: MessageSquare,
    bgColor: 'bg-gray-100',
    iconColor: 'text-gray-600',
  },
  experience: {
    icon: Sparkles,
    bgColor: 'bg-pink-100',
    iconColor: 'text-mereka-magenta',
  },
  jobs: {
    icon: Briefcase,
    bgColor: 'bg-blue-100',
    iconColor: 'text-mereka-blue',
  },
  system: {
    icon: Info,
    bgColor: 'bg-orange-100',
    iconColor: 'text-mereka-orange',
  },
}

interface NotificationCenterProps {
  notifications: Notification[]
  unreadCount: number
  onMarkAsRead: (id: string) => void
  onMarkAllAsRead: () => void
  onNotificationClick: (notification: Notification) => void
  onSettingsClick: () => void
}

export function NotificationCenter({
  notifications,
  unreadCount,
  onMarkAsRead,
  onMarkAllAsRead,
  onNotificationClick,
  onSettingsClick,
}: NotificationCenterProps) {
  const [isOpen, setIsOpen] = useState(false)
  const [filter, setFilter] = useState<'all' | 'unread'>('all')

  const filteredNotifications = filter === 'unread'
    ? notifications.filter((n) => !n.read)
    : notifications

  return (
    <div className="relative">
      {/* Trigger Button */}
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="relative p-2 rounded-full hover:bg-gray-100 transition-colors"
        aria-label={`Notifications${unreadCount > 0 ? `, ${unreadCount} unread` : ''}`}
      >
        <Bell className="w-6 h-6 text-gray-600" />
        {unreadCount > 0 && (
          <span className="absolute -top-1 -right-1 min-w-[20px] h-5 bg-red-500 text-white text-xs font-medium rounded-full flex items-center justify-center px-1">
            {unreadCount > 99 ? '99+' : unreadCount}
          </span>
        )}
      </button>

      {/* Dropdown */}
      {isOpen && (
        <>
          {/* Backdrop */}
          <div
            className="fixed inset-0 z-40"
            onClick={() => setIsOpen(false)}
          />

          {/* Panel */}
          <div className="absolute right-0 mt-2 w-[380px] max-h-[520px] bg-white rounded-xl shadow-xl border border-gray-200 z-50 flex flex-col overflow-hidden">
            {/* Header */}
            <div className="flex items-center justify-between p-4 border-b border-gray-200">
              <h2 className="font-heading text-h4 text-gray-900">
                Notifications
              </h2>
              <div className="flex items-center gap-2">
                {unreadCount > 0 && (
                  <button
                    onClick={onMarkAllAsRead}
                    className="text-body-sm text-mereka-teal hover:underline"
                  >
                    Mark all as read
                  </button>
                )}
                <button
                  onClick={onSettingsClick}
                  className="p-1.5 rounded-full hover:bg-gray-100 transition-colors"
                  aria-label="Notification settings"
                >
                  <Settings className="w-5 h-5 text-gray-400" />
                </button>
              </div>
            </div>

            {/* Filter Tabs */}
            <div className="flex border-b border-gray-200">
              <button
                onClick={() => setFilter('all')}
                className={`flex-1 py-2.5 text-body-sm font-medium transition-colors ${
                  filter === 'all'
                    ? 'text-mereka-teal border-b-2 border-mereka-teal'
                    : 'text-gray-500 hover:text-gray-700'
                }`}
              >
                All
              </button>
              <button
                onClick={() => setFilter('unread')}
                className={`flex-1 py-2.5 text-body-sm font-medium transition-colors ${
                  filter === 'unread'
                    ? 'text-mereka-teal border-b-2 border-mereka-teal'
                    : 'text-gray-500 hover:text-gray-700'
                }`}
              >
                Unread ({unreadCount})
              </button>
            </div>

            {/* Notification List */}
            <div className="flex-1 overflow-y-auto">
              {filteredNotifications.length === 0 ? (
                <NotificationEmptyState filter={filter} />
              ) : (
                <div className="divide-y divide-gray-100">
                  {filteredNotifications.map((notification) => (
                    <NotificationItem
                      key={notification.id}
                      notification={notification}
                      onMarkAsRead={() => onMarkAsRead(notification.id)}
                      onClick={() => {
                        onNotificationClick(notification)
                        setIsOpen(false)
                      }}
                    />
                  ))}
                </div>
              )}
            </div>

            {/* Footer */}
            {notifications.length > 0 && (
              <div className="p-3 border-t border-gray-200">
                <a
                  href="/notifications"
                  className="block text-center text-body-sm font-medium text-mereka-teal hover:underline"
                >
                  View all notifications
                </a>
              </div>
            )}
          </div>
        </>
      )}
    </div>
  )
}
```

---

## 3. Notification Items

Individual notification items for different categories.

### Base Notification Item

```tsx
interface NotificationItemProps {
  notification: Notification
  onMarkAsRead: () => void
  onClick: () => void
}

export function NotificationItem({
  notification,
  onMarkAsRead,
  onClick,
}: NotificationItemProps) {
  const config = categoryConfig[notification.category]
  const Icon = config.icon

  return (
    <div
      className={`flex gap-3 p-4 cursor-pointer transition-colors hover:bg-gray-50 ${
        !notification.read ? 'bg-mereka-teal/5' : ''
      }`}
      onClick={onClick}
      role="button"
      tabIndex={0}
    >
      {/* Icon or Image */}
      {notification.image ? (
        <img
          src={notification.image}
          alt=""
          className="w-10 h-10 rounded-full object-cover flex-shrink-0"
        />
      ) : (
        <div
          className={`w-10 h-10 rounded-full flex items-center justify-center flex-shrink-0 ${config.bgColor}`}
        >
          <Icon className={`w-5 h-5 ${config.iconColor}`} />
        </div>
      )}

      {/* Content */}
      <div className="flex-1 min-w-0">
        <p
          className={`text-body ${
            !notification.read ? 'font-medium text-gray-900' : 'text-gray-700'
          }`}
        >
          {notification.title}
        </p>
        <p className="text-body-sm text-gray-600 line-clamp-2 mt-0.5">
          {notification.message}
        </p>
        <p className="text-caption text-gray-400 mt-1">
          {formatRelativeTime(notification.timestamp)}
        </p>
      </div>

      {/* Unread Indicator + Actions */}
      <div className="flex items-center gap-2 flex-shrink-0">
        {!notification.read && (
          <button
            onClick={(e) => {
              e.stopPropagation()
              onMarkAsRead()
            }}
            className="p-1 rounded-full hover:bg-gray-200 transition-colors"
            aria-label="Mark as read"
          >
            <Check className="w-4 h-4 text-gray-400" />
          </button>
        )}
        {!notification.read && (
          <div className="w-2.5 h-2.5 bg-mereka-teal rounded-full" />
        )}
      </div>
    </div>
  )
}
```

### Category-Specific Notification Items

#### Booking Notifications

```tsx
type BookingNotificationType =
  | 'booking_confirmed'
  | 'booking_reminder'
  | 'booking_cancelled'
  | 'booking_rescheduled'

interface BookingNotificationProps {
  type: BookingNotificationType
  experienceTitle: string
  experienceImage: string
  date: Date
  time: string
  hostName: string
  read: boolean
  onClick: () => void
}

export function BookingNotification({
  type,
  experienceTitle,
  experienceImage,
  date,
  time,
  hostName,
  read,
  onClick,
}: BookingNotificationProps) {
  const config = {
    booking_confirmed: {
      title: 'Booking Confirmed',
      icon: CheckCircle,
      iconColor: 'text-mereka-forest',
      bgColor: 'bg-green-100',
    },
    booking_reminder: {
      title: 'Upcoming Experience',
      icon: Calendar,
      iconColor: 'text-mereka-blue',
      bgColor: 'bg-blue-100',
    },
    booking_cancelled: {
      title: 'Booking Cancelled',
      icon: XCircle,
      iconColor: 'text-mereka-burgundy',
      bgColor: 'bg-red-100',
    },
    booking_rescheduled: {
      title: 'Booking Rescheduled',
      icon: Clock,
      iconColor: 'text-mereka-orange',
      bgColor: 'bg-orange-100',
    },
  }

  const { title, icon: Icon, iconColor, bgColor } = config[type]

  return (
    <div
      className={`flex gap-3 p-4 cursor-pointer hover:bg-gray-50 transition-colors ${
        !read ? 'bg-mereka-teal/5' : ''
      }`}
      onClick={onClick}
    >
      {/* Experience Image */}
      <div className="relative flex-shrink-0">
        <img
          src={experienceImage}
          alt={experienceTitle}
          className="w-12 h-12 rounded-lg object-cover"
        />
        <div
          className={`absolute -bottom-1 -right-1 w-5 h-5 rounded-full flex items-center justify-center ${bgColor} border-2 border-white`}
        >
          <Icon className={`w-3 h-3 ${iconColor}`} />
        </div>
      </div>

      {/* Content */}
      <div className="flex-1 min-w-0">
        <p className={`text-body ${!read ? 'font-medium' : ''}`}>{title}</p>
        <p className="text-body-sm text-gray-900 font-medium truncate">
          {experienceTitle}
        </p>
        <p className="text-caption text-gray-500 mt-0.5">
          {formatDate(date)} at {time} with {hostName}
        </p>
      </div>

      {!read && <div className="w-2.5 h-2.5 bg-mereka-teal rounded-full flex-shrink-0 mt-2" />}
    </div>
  )
}
```

#### Payment Notifications

```tsx
type PaymentNotificationType =
  | 'payment_success'
  | 'payment_failed'
  | 'payment_refund'
  | 'payment_payout'

interface PaymentNotificationProps {
  type: PaymentNotificationType
  amount: number
  currency: string
  description: string
  read: boolean
  onClick: () => void
}

export function PaymentNotification({
  type,
  amount,
  currency,
  description,
  read,
  onClick,
}: PaymentNotificationProps) {
  const config = {
    payment_success: {
      title: 'Payment Successful',
      icon: CheckCircle,
      iconColor: 'text-mereka-forest',
      bgColor: 'bg-green-100',
      amountColor: 'text-mereka-forest',
      prefix: '-',
    },
    payment_failed: {
      title: 'Payment Failed',
      icon: XCircle,
      iconColor: 'text-mereka-burgundy',
      bgColor: 'bg-red-100',
      amountColor: 'text-mereka-burgundy',
      prefix: '',
    },
    payment_refund: {
      title: 'Refund Processed',
      icon: RefreshCw,
      iconColor: 'text-mereka-blue',
      bgColor: 'bg-blue-100',
      amountColor: 'text-mereka-blue',
      prefix: '+',
    },
    payment_payout: {
      title: 'Payout Received',
      icon: Wallet,
      iconColor: 'text-mereka-forest',
      bgColor: 'bg-green-100',
      amountColor: 'text-mereka-forest',
      prefix: '+',
    },
  }

  const { title, icon: Icon, iconColor, bgColor, amountColor, prefix } = config[type]

  return (
    <div
      className={`flex gap-3 p-4 cursor-pointer hover:bg-gray-50 transition-colors ${
        !read ? 'bg-mereka-teal/5' : ''
      }`}
      onClick={onClick}
    >
      <div className={`w-10 h-10 rounded-full flex items-center justify-center ${bgColor}`}>
        <Icon className={`w-5 h-5 ${iconColor}`} />
      </div>

      <div className="flex-1 min-w-0">
        <p className={`text-body ${!read ? 'font-medium' : ''}`}>{title}</p>
        <p className="text-body-sm text-gray-600 truncate">{description}</p>
      </div>

      <div className="text-right flex-shrink-0">
        <p className={`font-heading text-h5 ${amountColor}`}>
          {prefix}{currency} {amount.toLocaleString()}
        </p>
        {!read && <div className="w-2.5 h-2.5 bg-mereka-teal rounded-full ml-auto mt-1" />}
      </div>
    </div>
  )
}
```

#### Hub Notifications

```tsx
type HubNotificationType =
  | 'hub_invitation'
  | 'hub_approved'
  | 'hub_member_joined'
  | 'hub_role_changed'

interface HubNotificationProps {
  type: HubNotificationType
  hubName: string
  hubLogo: string
  actorName?: string
  actorAvatar?: string
  role?: string
  read: boolean
  onClick: () => void
}

export function HubNotification({
  type,
  hubName,
  hubLogo,
  actorName,
  actorAvatar,
  role,
  read,
  onClick,
}: HubNotificationProps) {
  const getMessage = () => {
    switch (type) {
      case 'hub_invitation':
        return `You've been invited to join ${hubName}`
      case 'hub_approved':
        return `Your hub "${hubName}" has been approved!`
      case 'hub_member_joined':
        return `${actorName} joined ${hubName}`
      case 'hub_role_changed':
        return `Your role at ${hubName} has been changed to ${role}`
      default:
        return ''
    }
  }

  return (
    <div
      className={`flex gap-3 p-4 cursor-pointer hover:bg-gray-50 transition-colors ${
        !read ? 'bg-mereka-teal/5' : ''
      }`}
      onClick={onClick}
    >
      <div className="relative flex-shrink-0">
        <img
          src={hubLogo}
          alt={hubName}
          className="w-10 h-10 rounded-lg object-cover"
        />
        {actorAvatar && (
          <img
            src={actorAvatar}
            alt={actorName}
            className="absolute -bottom-1 -right-1 w-5 h-5 rounded-full border-2 border-white"
          />
        )}
      </div>

      <div className="flex-1 min-w-0">
        <p className={`text-body ${!read ? 'font-medium' : ''}`}>
          {getMessage()}
        </p>
        <p className="text-caption text-gray-400 mt-1">
          {formatRelativeTime(new Date())}
        </p>
      </div>

      {type === 'hub_invitation' && (
        <div className="flex gap-2 flex-shrink-0">
          <button className="px-3 py-1.5 text-body-sm font-medium text-gray-600 hover:bg-gray-100 rounded-full transition-colors">
            Decline
          </button>
          <button className="px-3 py-1.5 text-body-sm font-medium bg-mereka-teal text-white rounded-full hover:bg-mereka-teal/90 transition-colors">
            Accept
          </button>
        </div>
      )}

      {type !== 'hub_invitation' && !read && (
        <div className="w-2.5 h-2.5 bg-mereka-teal rounded-full flex-shrink-0 mt-2" />
      )}
    </div>
  )
}
```

#### Message Notifications

```tsx
interface MessageNotificationProps {
  senderName: string
  senderAvatar: string
  preview: string
  unreadCount?: number
  read: boolean
  onClick: () => void
}

export function MessageNotification({
  senderName,
  senderAvatar,
  preview,
  unreadCount,
  read,
  onClick,
}: MessageNotificationProps) {
  return (
    <div
      className={`flex gap-3 p-4 cursor-pointer hover:bg-gray-50 transition-colors ${
        !read ? 'bg-mereka-teal/5' : ''
      }`}
      onClick={onClick}
    >
      <div className="relative flex-shrink-0">
        <img
          src={senderAvatar}
          alt={senderName}
          className="w-10 h-10 rounded-full object-cover"
        />
        {!read && (
          <div className="absolute -top-0.5 -right-0.5 w-3 h-3 bg-mereka-teal rounded-full border-2 border-white" />
        )}
      </div>

      <div className="flex-1 min-w-0">
        <div className="flex items-center gap-2">
          <span className={`text-body ${!read ? 'font-medium' : ''}`}>
            {senderName}
          </span>
          {unreadCount && unreadCount > 1 && (
            <span className="text-caption bg-mereka-teal text-white px-1.5 py-0.5 rounded-full">
              {unreadCount}
            </span>
          )}
        </div>
        <p className="text-body-sm text-gray-600 truncate">{preview}</p>
      </div>
    </div>
  )
}
```

#### Experience Notifications

```tsx
type ExperienceNotificationType =
  | 'experience_approved'
  | 'experience_rejected'
  | 'experience_expired'
  | 'experience_featured'

interface ExperienceNotificationProps {
  type: ExperienceNotificationType
  experienceTitle: string
  experienceImage: string
  reason?: string
  read: boolean
  onClick: () => void
}

export function ExperienceNotification({
  type,
  experienceTitle,
  experienceImage,
  reason,
  read,
  onClick,
}: ExperienceNotificationProps) {
  const config = {
    experience_approved: {
      title: 'Experience Approved',
      icon: CheckCircle,
      iconColor: 'text-mereka-forest',
      bgColor: 'bg-green-100',
    },
    experience_rejected: {
      title: 'Experience Rejected',
      icon: XCircle,
      iconColor: 'text-mereka-burgundy',
      bgColor: 'bg-red-100',
    },
    experience_expired: {
      title: 'Experience Expired',
      icon: Clock,
      iconColor: 'text-gray-500',
      bgColor: 'bg-gray-100',
    },
    experience_featured: {
      title: 'Your Experience is Featured!',
      icon: Sparkles,
      iconColor: 'text-mereka-magenta',
      bgColor: 'bg-pink-100',
    },
  }

  const { title, icon: Icon, iconColor, bgColor } = config[type]

  return (
    <div
      className={`flex gap-3 p-4 cursor-pointer hover:bg-gray-50 transition-colors ${
        !read ? 'bg-mereka-teal/5' : ''
      }`}
      onClick={onClick}
    >
      <div className="relative flex-shrink-0">
        <img
          src={experienceImage}
          alt={experienceTitle}
          className="w-12 h-12 rounded-lg object-cover"
        />
        <div
          className={`absolute -bottom-1 -right-1 w-5 h-5 rounded-full flex items-center justify-center ${bgColor} border-2 border-white`}
        >
          <Icon className={`w-3 h-3 ${iconColor}`} />
        </div>
      </div>

      <div className="flex-1 min-w-0">
        <p className={`text-body ${!read ? 'font-medium' : ''}`}>{title}</p>
        <p className="text-body-sm text-gray-900 font-medium truncate">
          {experienceTitle}
        </p>
        {reason && (
          <p className="text-caption text-gray-500 mt-0.5">{reason}</p>
        )}
      </div>

      {!read && <div className="w-2.5 h-2.5 bg-mereka-teal rounded-full flex-shrink-0 mt-2" />}
    </div>
  )
}
```

#### Jobs Notifications

```tsx
type JobsNotificationType =
  | 'proposal_received'
  | 'offer_sent'
  | 'work_submitted'
  | 'job_completed'

interface JobsNotificationProps {
  type: JobsNotificationType
  jobTitle: string
  actorName: string
  actorAvatar: string
  amount?: number
  currency?: string
  read: boolean
  onClick: () => void
}

export function JobsNotification({
  type,
  jobTitle,
  actorName,
  actorAvatar,
  amount,
  currency,
  read,
  onClick,
}: JobsNotificationProps) {
  const getMessage = () => {
    switch (type) {
      case 'proposal_received':
        return `${actorName} submitted a proposal for "${jobTitle}"`
      case 'offer_sent':
        return `Your offer for "${jobTitle}" was sent to ${actorName}`
      case 'work_submitted':
        return `${actorName} submitted work for "${jobTitle}"`
      case 'job_completed':
        return `"${jobTitle}" has been marked as completed`
      default:
        return ''
    }
  }

  return (
    <div
      className={`flex gap-3 p-4 cursor-pointer hover:bg-gray-50 transition-colors ${
        !read ? 'bg-mereka-teal/5' : ''
      }`}
      onClick={onClick}
    >
      <img
        src={actorAvatar}
        alt={actorName}
        className="w-10 h-10 rounded-full object-cover flex-shrink-0"
      />

      <div className="flex-1 min-w-0">
        <p className={`text-body ${!read ? 'font-medium' : ''}`}>
          {getMessage()}
        </p>
        {amount && currency && (
          <p className="text-body-sm text-mereka-forest font-medium mt-0.5">
            {currency} {amount.toLocaleString()}
          </p>
        )}
        <p className="text-caption text-gray-400 mt-1">
          {formatRelativeTime(new Date())}
        </p>
      </div>

      {!read && <div className="w-2.5 h-2.5 bg-mereka-teal rounded-full flex-shrink-0 mt-2" />}
    </div>
  )
}
```

#### System Notifications

```tsx
type SystemNotificationType =
  | 'maintenance'
  | 'update'
  | 'security_alert'

interface SystemNotificationProps {
  type: SystemNotificationType
  title: string
  message: string
  scheduledTime?: Date
  read: boolean
  onClick: () => void
}

export function SystemNotification({
  type,
  title,
  message,
  scheduledTime,
  read,
  onClick,
}: SystemNotificationProps) {
  const config = {
    maintenance: {
      icon: Wrench,
      iconColor: 'text-mereka-orange',
      bgColor: 'bg-orange-100',
    },
    update: {
      icon: Download,
      iconColor: 'text-mereka-blue',
      bgColor: 'bg-blue-100',
    },
    security_alert: {
      icon: Shield,
      iconColor: 'text-mereka-burgundy',
      bgColor: 'bg-red-100',
    },
  }

  const { icon: Icon, iconColor, bgColor } = config[type]

  return (
    <div
      className={`flex gap-3 p-4 cursor-pointer hover:bg-gray-50 transition-colors ${
        !read ? 'bg-orange-50' : ''
      }`}
      onClick={onClick}
    >
      <div className={`w-10 h-10 rounded-full flex items-center justify-center ${bgColor}`}>
        <Icon className={`w-5 h-5 ${iconColor}`} />
      </div>

      <div className="flex-1 min-w-0">
        <p className={`text-body ${!read ? 'font-medium' : ''}`}>{title}</p>
        <p className="text-body-sm text-gray-600 line-clamp-2">{message}</p>
        {scheduledTime && (
          <p className="text-caption text-mereka-orange font-medium mt-1">
            Scheduled: {formatDateTime(scheduledTime)}
          </p>
        )}
      </div>

      {!read && <div className="w-2.5 h-2.5 bg-mereka-orange rounded-full flex-shrink-0 mt-2" />}
    </div>
  )
}
```

---

## 4. Empty Notification State

When there are no notifications to display.

```tsx
interface NotificationEmptyStateProps {
  filter: 'all' | 'unread'
}

export function NotificationEmptyState({ filter }: NotificationEmptyStateProps) {
  return (
    <div className="flex flex-col items-center justify-center py-12 px-6 text-center">
      <div className="w-16 h-16 bg-gray-100 rounded-full flex items-center justify-center mb-4">
        <Bell className="w-8 h-8 text-gray-400" />
      </div>

      {filter === 'unread' ? (
        <>
          <h3 className="font-heading text-h5 text-gray-900">All caught up!</h3>
          <p className="text-body-sm text-gray-500 mt-1">
            You've read all your notifications.
          </p>
        </>
      ) : (
        <>
          <h3 className="font-heading text-h5 text-gray-900">No notifications yet</h3>
          <p className="text-body-sm text-gray-500 mt-1">
            When you have new activity, it will appear here.
          </p>
        </>
      )}
    </div>
  )
}
```

---

## 5. Notification Badges

### Badge Types

| Type | Usage | Visual |
|------|-------|--------|
| **Dot** | Indicates new/unread | Small colored circle |
| **Count** | Shows number of items | Number in circle |
| **Count (capped)** | Large counts | "99+" format |

### Badge Components

```tsx
// Simple dot badge
interface DotBadgeProps {
  color?: 'red' | 'teal' | 'orange' | 'blue'
  size?: 'sm' | 'md' | 'lg'
}

export function DotBadge({ color = 'red', size = 'md' }: DotBadgeProps) {
  const colorClasses = {
    red: 'bg-red-500',
    teal: 'bg-mereka-teal',
    orange: 'bg-mereka-orange',
    blue: 'bg-mereka-blue',
  }

  const sizeClasses = {
    sm: 'w-2 h-2',
    md: 'w-2.5 h-2.5',
    lg: 'w-3 h-3',
  }

  return (
    <span
      className={`rounded-full ${colorClasses[color]} ${sizeClasses[size]}`}
      aria-hidden="true"
    />
  )
}

// Count badge
interface CountBadgeProps {
  count: number
  max?: number
  color?: 'red' | 'teal' | 'gray'
  size?: 'sm' | 'md'
}

export function CountBadge({
  count,
  max = 99,
  color = 'red',
  size = 'md',
}: CountBadgeProps) {
  if (count === 0) return null

  const displayCount = count > max ? `${max}+` : count

  const colorClasses = {
    red: 'bg-red-500 text-white',
    teal: 'bg-mereka-teal text-white',
    gray: 'bg-gray-500 text-white',
  }

  const sizeClasses = {
    sm: 'min-w-[16px] h-4 text-[10px] px-1',
    md: 'min-w-[20px] h-5 text-xs px-1.5',
  }

  return (
    <span
      className={`inline-flex items-center justify-center font-medium rounded-full ${colorClasses[color]} ${sizeClasses[size]}`}
      aria-label={`${count} notifications`}
    >
      {displayCount}
    </span>
  )
}

// Badge wrapper for icons
interface IconWithBadgeProps {
  icon: LucideIcon
  count?: number
  showDot?: boolean
  onClick?: () => void
  ariaLabel: string
}

export function IconWithBadge({
  icon: Icon,
  count,
  showDot,
  onClick,
  ariaLabel,
}: IconWithBadgeProps) {
  return (
    <button
      onClick={onClick}
      className="relative p-2 rounded-full hover:bg-gray-100 transition-colors"
      aria-label={ariaLabel}
    >
      <Icon className="w-6 h-6 text-gray-600" />

      {/* Dot badge */}
      {showDot && !count && (
        <span className="absolute top-1.5 right-1.5 w-2.5 h-2.5 bg-red-500 rounded-full border-2 border-white" />
      )}

      {/* Count badge */}
      {count !== undefined && count > 0 && (
        <span className="absolute -top-1 -right-1 min-w-[20px] h-5 bg-red-500 text-white text-xs font-medium rounded-full flex items-center justify-center px-1">
          {count > 99 ? '99+' : count}
        </span>
      )}
    </button>
  )
}
```

### Usage Examples

```tsx
// Notification bell with count
<IconWithBadge
  icon={Bell}
  count={5}
  ariaLabel="5 notifications"
  onClick={() => setNotificationOpen(true)}
/>

// Message icon with unread dot
<IconWithBadge
  icon={MessageSquare}
  showDot
  ariaLabel="New messages"
  onClick={() => navigate('/messages')}
/>

// Inline badge in navigation
<nav className="flex items-center gap-6">
  <a href="/bookings" className="flex items-center gap-2">
    <Calendar className="w-5 h-5" />
    Bookings
    <CountBadge count={2} color="teal" size="sm" />
  </a>
</nav>

// Badge in tab
<div className="flex gap-1">
  <button className="flex items-center gap-2 px-4 py-2 border-b-2 border-mereka-teal">
    Inbox
    <CountBadge count={12} size="sm" />
  </button>
  <button className="flex items-center gap-2 px-4 py-2 text-gray-500">
    Archived
  </button>
</div>
```

---

## 6. Push Notification Patterns

Patterns for browser and mobile push notifications.

### Push Notification Preview

```tsx
interface PushNotificationPreviewProps {
  appIcon: string
  appName: string
  title: string
  body: string
  image?: string
  timestamp: string
  platform: 'ios' | 'android' | 'web'
}

export function PushNotificationPreview({
  appIcon,
  appName,
  title,
  body,
  image,
  timestamp,
  platform,
}: PushNotificationPreviewProps) {
  if (platform === 'ios') {
    return (
      <div className="w-[340px] bg-white/90 backdrop-blur-xl rounded-2xl shadow-lg overflow-hidden">
        <div className="p-3">
          <div className="flex items-center gap-2 mb-2">
            <img src={appIcon} alt="" className="w-5 h-5 rounded" />
            <span className="text-caption text-gray-500 uppercase tracking-wide">
              {appName}
            </span>
            <span className="text-caption text-gray-400 ml-auto">{timestamp}</span>
          </div>
          <p className="font-medium text-gray-900 text-body">{title}</p>
          <p className="text-body-sm text-gray-600 line-clamp-2 mt-0.5">{body}</p>
        </div>
        {image && (
          <img src={image} alt="" className="w-full h-32 object-cover" />
        )}
      </div>
    )
  }

  if (platform === 'android') {
    return (
      <div className="w-[340px] bg-gray-900 rounded-xl shadow-lg overflow-hidden">
        <div className="p-4">
          <div className="flex items-center gap-3">
            <img src={appIcon} alt="" className="w-6 h-6 rounded" />
            <span className="text-body-sm text-gray-400">{appName}</span>
            <span className="text-caption text-gray-500 ml-auto">{timestamp}</span>
          </div>
          <p className="font-medium text-white text-body mt-2">{title}</p>
          <p className="text-body-sm text-gray-400 line-clamp-2 mt-1">{body}</p>
        </div>
        {image && (
          <img src={image} alt="" className="w-full h-32 object-cover" />
        )}
      </div>
    )
  }

  // Web (browser notification)
  return (
    <div className="w-[360px] bg-white rounded-lg shadow-xl border border-gray-200 overflow-hidden">
      <div className="p-4 flex gap-3">
        <img src={appIcon} alt="" className="w-12 h-12 rounded" />
        <div className="flex-1 min-w-0">
          <div className="flex items-center justify-between">
            <span className="text-caption text-gray-500">{appName}</span>
            <span className="text-caption text-gray-400">{timestamp}</span>
          </div>
          <p className="font-medium text-gray-900 mt-1">{title}</p>
          <p className="text-body-sm text-gray-600 line-clamp-2">{body}</p>
        </div>
        {image && (
          <img src={image} alt="" className="w-16 h-16 rounded object-cover" />
        )}
      </div>
    </div>
  )
}
```

### Push Notification Content Templates

```tsx
// Notification content by type
const pushNotificationTemplates = {
  booking_confirmed: {
    title: 'Booking Confirmed!',
    body: (data: any) => `Your booking for "${data.experienceTitle}" on ${data.date} is confirmed.`,
  },
  booking_reminder: {
    title: 'Upcoming Experience',
    body: (data: any) => `Reminder: "${data.experienceTitle}" starts in ${data.timeUntil}.`,
  },
  payment_success: {
    title: 'Payment Successful',
    body: (data: any) => `Your payment of ${data.currency} ${data.amount} was processed.`,
  },
  new_message: {
    title: (data: any) => `Message from ${data.senderName}`,
    body: (data: any) => data.preview,
  },
  hub_invitation: {
    title: 'Hub Invitation',
    body: (data: any) => `You've been invited to join "${data.hubName}"`,
  },
}
```

---

## 7. Email Notification Preview Cards

In-app preview cards for email notifications.

### Email Preview Card

```tsx
import {
  Mail,
  Calendar,
  CreditCard,
  Building2,
  MessageSquare,
  ExternalLink,
} from 'lucide-react'

interface EmailPreviewCardProps {
  subject: string
  previewText: string
  category: NotificationCategory
  sentAt: Date
  read: boolean
  onView: () => void
  onOpenEmail: () => void
}

export function EmailPreviewCard({
  subject,
  previewText,
  category,
  sentAt,
  read,
  onView,
  onOpenEmail,
}: EmailPreviewCardProps) {
  const config = categoryConfig[category]
  const Icon = config.icon

  return (
    <div
      className={`bg-white rounded-xl border p-4 transition-all hover:shadow-md ${
        !read ? 'border-mereka-teal/30 bg-mereka-teal/5' : 'border-gray-200'
      }`}
    >
      <div className="flex items-start gap-3">
        {/* Email icon */}
        <div
          className={`w-10 h-10 rounded-full flex items-center justify-center ${config.bgColor}`}
        >
          <Icon className={`w-5 h-5 ${config.iconColor}`} />
        </div>

        {/* Content */}
        <div className="flex-1 min-w-0">
          <div className="flex items-start justify-between gap-2">
            <p className={`text-body ${!read ? 'font-medium text-gray-900' : 'text-gray-700'}`}>
              {subject}
            </p>
            <span className="text-caption text-gray-400 flex-shrink-0">
              {formatRelativeTime(sentAt)}
            </span>
          </div>
          <p className="text-body-sm text-gray-500 line-clamp-2 mt-1">
            {previewText}
          </p>

          {/* Actions */}
          <div className="flex items-center gap-3 mt-3">
            <button
              onClick={onView}
              className="text-body-sm font-medium text-mereka-teal hover:underline"
            >
              View Details
            </button>
            <button
              onClick={onOpenEmail}
              className="text-body-sm text-gray-500 hover:text-gray-700 flex items-center gap-1"
            >
              <Mail className="w-4 h-4" />
              Open Email
              <ExternalLink className="w-3 h-3" />
            </button>
          </div>
        </div>

        {/* Unread indicator */}
        {!read && (
          <div className="w-2.5 h-2.5 bg-mereka-teal rounded-full flex-shrink-0" />
        )}
      </div>
    </div>
  )
}
```

### Email Notification List

```tsx
interface EmailNotificationListProps {
  emails: EmailPreviewCardProps[]
  onLoadMore: () => void
  hasMore: boolean
}

export function EmailNotificationList({
  emails,
  onLoadMore,
  hasMore,
}: EmailNotificationListProps) {
  return (
    <div className="space-y-3">
      {/* Header */}
      <div className="flex items-center justify-between">
        <h2 className="font-heading text-h4 text-gray-900">Email Notifications</h2>
        <a
          href="/settings/notifications"
          className="text-body-sm text-mereka-teal hover:underline"
        >
          Manage preferences
        </a>
      </div>

      {/* Email list */}
      {emails.length === 0 ? (
        <div className="text-center py-8">
          <Mail className="w-12 h-12 text-gray-300 mx-auto mb-3" />
          <p className="text-body text-gray-500">No email notifications</p>
        </div>
      ) : (
        <>
          <div className="space-y-2">
            {emails.map((email, index) => (
              <EmailPreviewCard key={index} {...email} />
            ))}
          </div>

          {hasMore && (
            <button
              onClick={onLoadMore}
              className="w-full py-3 text-body-sm font-medium text-gray-600 hover:text-gray-900 hover:bg-gray-50 rounded-lg transition-colors"
            >
              Load more
            </button>
          )}
        </>
      )}
    </div>
  )
}
```

### Email Template Preview

For showing what the actual email looks like:

```tsx
interface EmailTemplatePreviewProps {
  type: string
  subject: string
  previewHtml: string
}

export function EmailTemplatePreview({
  type,
  subject,
  previewHtml,
}: EmailTemplatePreviewProps) {
  return (
    <div className="bg-white rounded-xl border border-gray-200 overflow-hidden">
      {/* Email header */}
      <div className="bg-gray-50 px-6 py-4 border-b border-gray-200">
        <div className="flex items-center gap-3 mb-3">
          <img
            src="/mereka-logo.svg"
            alt="Mereka"
            className="h-6"
          />
          <span className="text-caption text-gray-500">noreply@mereka.io</span>
        </div>
        <p className="font-medium text-gray-900">{subject}</p>
      </div>

      {/* Email body preview */}
      <div
        className="p-6 max-h-[400px] overflow-y-auto"
        dangerouslySetInnerHTML={{ __html: previewHtml }}
      />

      {/* Footer */}
      <div className="bg-gray-50 px-6 py-3 border-t border-gray-200 flex items-center justify-between">
        <span className="text-caption text-gray-400">
          Email preview - {type}
        </span>
        <button className="text-body-sm text-mereka-teal hover:underline flex items-center gap-1">
          <ExternalLink className="w-4 h-4" />
          Open full email
        </button>
      </div>
    </div>
  )
}
```

---

## Notification Type Reference

Complete mapping of backend notification types to UI components:

### Booking Notifications

| Backend Type | Title | Category | Icon | Color |
|--------------|-------|----------|------|-------|
| `booking.created` | Booking Request Sent | booking | `Calendar` | Blue |
| `booking.confirmed` | Booking Confirmed | booking | `CheckCircle` | Green |
| `booking.reminder` | Upcoming Experience | booking | `Clock` | Blue |
| `booking.cancelled` | Booking Cancelled | booking | `XCircle` | Red |
| `booking.rescheduled` | Booking Rescheduled | booking | `RefreshCw` | Orange |
| `booking.completed` | Experience Completed | booking | `Check` | Gray |
| `booking.review_requested` | Leave a Review | booking | `Star` | Gold |

### Payment Notifications

| Backend Type | Title | Category | Icon | Color |
|--------------|-------|----------|------|-------|
| `payment.success` | Payment Successful | payment | `CheckCircle` | Green |
| `payment.failed` | Payment Failed | payment | `XCircle` | Red |
| `payment.refund_initiated` | Refund Processing | payment | `RefreshCw` | Blue |
| `payment.refund_completed` | Refund Completed | payment | `Check` | Green |
| `payment.payout_sent` | Payout Sent | payment | `Wallet` | Green |
| `payment.payout_received` | Payout Received | payment | `Wallet` | Green |
| `payment.invoice_ready` | Invoice Available | payment | `Receipt` | Gray |

### Hub Notifications

| Backend Type | Title | Category | Icon | Color |
|--------------|-------|----------|------|-------|
| `hub.invitation` | Hub Invitation | hub | `UserPlus` | Teal |
| `hub.invitation_accepted` | Invitation Accepted | hub | `CheckCircle` | Green |
| `hub.approved` | Hub Approved | hub | `CheckCircle` | Green |
| `hub.rejected` | Hub Application Rejected | hub | `XCircle` | Red |
| `hub.member_joined` | New Team Member | hub | `UserPlus` | Teal |
| `hub.member_left` | Member Left | hub | `UserMinus` | Gray |
| `hub.role_changed` | Role Updated | hub | `Shield` | Blue |
| `hub.experience_published` | Experience Published | hub | `Sparkles` | Magenta |

### Message Notifications

| Backend Type | Title | Category | Icon | Color |
|--------------|-------|----------|------|-------|
| `message.new` | New Message | message | `MessageSquare` | Gray |
| `message.mention` | You were mentioned | message | `AtSign` | Blue |
| `message.digest` | Unread Messages | message | `Mail` | Gray |

### Experience Notifications

| Backend Type | Title | Category | Icon | Color |
|--------------|-------|----------|------|-------|
| `experience.approved` | Experience Approved | experience | `CheckCircle` | Green |
| `experience.rejected` | Experience Rejected | experience | `XCircle` | Red |
| `experience.featured` | Experience Featured | experience | `Sparkles` | Magenta |
| `experience.expired` | Experience Expired | experience | `Clock` | Gray |
| `experience.review_received` | New Review | experience | `Star` | Gold |
| `experience.sold_out` | Experience Sold Out | experience | `Users` | Orange |

### Jobs Notifications

| Backend Type | Title | Category | Icon | Color |
|--------------|-------|----------|------|-------|
| `job.proposal_received` | New Proposal | jobs | `FileText` | Blue |
| `job.proposal_accepted` | Proposal Accepted | jobs | `CheckCircle` | Green |
| `job.proposal_rejected` | Proposal Declined | jobs | `XCircle` | Red |
| `job.offer_sent` | Offer Sent | jobs | `Send` | Blue |
| `job.offer_accepted` | Offer Accepted | jobs | `CheckCircle` | Green |
| `job.work_submitted` | Work Submitted | jobs | `Upload` | Blue |
| `job.work_approved` | Work Approved | jobs | `CheckCircle` | Green |
| `job.completed` | Job Completed | jobs | `Trophy` | Gold |

### System Notifications

| Backend Type | Title | Category | Icon | Color |
|--------------|-------|----------|------|-------|
| `system.maintenance` | Scheduled Maintenance | system | `Wrench` | Orange |
| `system.update` | Platform Update | system | `Download` | Blue |
| `system.security_alert` | Security Alert | system | `Shield` | Red |
| `system.welcome` | Welcome to Mereka | system | `Sparkles` | Teal |
| `system.profile_incomplete` | Complete Your Profile | system | `User` | Orange |

---

## Accessibility

### ARIA Attributes

```tsx
// Toast notifications
<div role="alert" aria-live="polite">
  <Toast variant="success" title="Booking confirmed!" />
</div>

// Notification center
<button
  aria-label={`Notifications${unreadCount > 0 ? `, ${unreadCount} unread` : ''}`}
  aria-expanded={isOpen}
  aria-haspopup="true"
>
  <Bell />
</button>

// Notification list
<ul role="list" aria-label="Notifications">
  <li role="listitem" aria-current={!notification.read ? 'true' : undefined}>
    {/* notification content */}
  </li>
</ul>

// Badge counts
<span aria-label={`${count} new notifications`}>
  {count}
</span>
```

### Keyboard Navigation

```tsx
// Notification dropdown keyboard support
function NotificationDropdown() {
  const handleKeyDown = (e: KeyboardEvent) => {
    switch (e.key) {
      case 'Escape':
        setIsOpen(false)
        break
      case 'ArrowDown':
        focusNextItem()
        break
      case 'ArrowUp':
        focusPreviousItem()
        break
      case 'Enter':
        activateCurrentItem()
        break
    }
  }

  return (
    <div onKeyDown={handleKeyDown} tabIndex={-1}>
      {/* dropdown content */}
    </div>
  )
}
```

### Screen Reader Announcements

```tsx
// Live region for new notifications
<div
  role="status"
  aria-live="polite"
  aria-atomic="true"
  className="sr-only"
>
  {newNotificationAnnouncement}
</div>

// Usage
setNewNotificationAnnouncement('New booking notification: Your booking has been confirmed.')
```

---

## Animation & Transitions

### Toast Animations

```css
/* Toast enter animation */
@keyframes toast-enter {
  from {
    opacity: 0;
    transform: translateY(-16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Toast exit animation */
@keyframes toast-exit {
  from {
    opacity: 1;
    transform: translateY(0);
  }
  to {
    opacity: 0;
    transform: translateY(-16px);
  }
}

.toast-enter {
  animation: toast-enter 0.3s ease-out;
}

.toast-exit {
  animation: toast-exit 0.2s ease-in;
}
```

### Notification Badge Animation

```tsx
// Pulse animation for new notifications
<span className="absolute -top-1 -right-1 w-5 h-5 bg-red-500 rounded-full animate-pulse">
  <span className="absolute inset-0 bg-red-500 rounded-full animate-ping opacity-75" />
</span>
```

### Dropdown Animation

```tsx
// Tailwind animation classes
<div className={`
  transition-all duration-200 ease-out
  ${isOpen
    ? 'opacity-100 translate-y-0 scale-100'
    : 'opacity-0 -translate-y-2 scale-95 pointer-events-none'
  }
`}>
  {/* dropdown content */}
</div>
```

---

## Don'ts

- **Don't auto-dismiss error toasts** - Users need time to read and act on errors
- **Don't stack too many toasts** - Limit to 3-5 visible at once
- **Don't use notifications for marketing** - Reserved for user-relevant updates
- **Don't skip unread indicators** - Users expect visual distinction
- **Don't block interactions** - Notifications should be dismissible
- **Don't use sound without permission** - Respect user preferences
- **Don't lose notifications on refresh** - Persist to backend
- **Don't forget mobile responsiveness** - Toasts should adapt to screen size
- **Don't use inconsistent icons** - Stick to the category mapping
- **Don't omit timestamps** - Users need temporal context

---

## Helper Functions

```tsx
// Format relative time
export function formatRelativeTime(date: Date): string {
  const now = new Date()
  const diffMs = now.getTime() - date.getTime()
  const diffSeconds = Math.floor(diffMs / 1000)
  const diffMinutes = Math.floor(diffSeconds / 60)
  const diffHours = Math.floor(diffMinutes / 60)
  const diffDays = Math.floor(diffHours / 24)

  if (diffSeconds < 60) return 'Just now'
  if (diffMinutes < 60) return `${diffMinutes}m ago`
  if (diffHours < 24) return `${diffHours}h ago`
  if (diffDays < 7) return `${diffDays}d ago`

  return date.toLocaleDateString('en-MY', {
    month: 'short',
    day: 'numeric',
  })
}

// Format date
export function formatDate(date: Date): string {
  return date.toLocaleDateString('en-MY', {
    weekday: 'short',
    month: 'short',
    day: 'numeric',
  })
}

// Format date and time
export function formatDateTime(date: Date): string {
  return date.toLocaleDateString('en-MY', {
    weekday: 'short',
    month: 'short',
    day: 'numeric',
    hour: 'numeric',
    minute: '2-digit',
  })
}
```

---

## Quick Import Reference

```tsx
import {
  // Icons for categories
  Calendar,
  CreditCard,
  Building2,
  MessageSquare,
  Sparkles,
  Briefcase,
  Info,

  // Icons for status
  CheckCircle,
  XCircle,
  AlertTriangle,
  Clock,
  RefreshCw,

  // Icons for actions
  Bell,
  Settings,
  Check,
  X,
  Mail,
  ExternalLink,

  // Icons for payment
  Wallet,
  Receipt,

  // Icons for hub
  UserPlus,
  UserMinus,
  Shield,

  // Icons for jobs
  FileText,
  Send,
  Upload,
  Trophy,

  // Icons for system
  Wrench,
  Download,

  // Icons for experience
  Star,
  Users,
} from 'lucide-react'
```
