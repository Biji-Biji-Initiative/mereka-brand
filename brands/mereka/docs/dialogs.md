# Dialogs

> Dialogs (modals) interrupt user flow to deliver important information or collect input. Mereka uses **Radix UI Dialog** as the foundation for accessible, composable modal components.

**Figma Section**: Dialogs, Overlays

---

## Dialog Types Overview

| Type | Purpose | Example |
|------|---------|---------|
| **Alert Dialog** | Confirmations, warnings, destructive actions | "Cancel Booking?", "Delete Experience?" |
| **Form Dialog** | Data entry within modal context | "Add Team Member", "Edit Profile" |
| **Content Dialog** | Detail views, previews, read-only content | Image preview, Terms of Service |

---

## Dialog Anatomy

```
Dialog
├── Backdrop (overlay)
├── Container
│   ├── Header
│   │   ├── Title
│   │   ├── Description (optional)
│   │   └── Close Button (optional)
│   ├── Content
│   │   └── (form fields, text, images, etc.)
│   └── Footer
│       └── Action Buttons
```

### Spacing Guidelines

| Element | Desktop | Mobile |
|---------|---------|--------|
| **Container padding** | 24px | 20px |
| **Header bottom margin** | 16px | 12px |
| **Content vertical spacing** | 24px | 20px |
| **Footer top margin** | 24px | 20px |
| **Button gap** | 12px | 12px |

---

## Base Dialog Component

The foundation component using Radix UI Dialog with Tailwind styling.

```tsx
import * as DialogPrimitive from '@radix-ui/react-dialog'
import { X } from 'lucide-react'
import { cva, type VariantProps } from 'class-variance-authority'

const dialogOverlayVariants = cva(
  'fixed inset-0 z-50 bg-black/50 backdrop-blur-sm',
  {
    variants: {
      animation: {
        fade: 'data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0',
        none: '',
      },
    },
    defaultVariants: {
      animation: 'fade',
    },
  }
)

const dialogContentVariants = cva(
  'fixed z-50 bg-white shadow-xl focus:outline-none',
  {
    variants: {
      size: {
        sm: 'w-full max-w-sm',
        md: 'w-full max-w-md',
        lg: 'w-full max-w-lg',
        xl: 'w-full max-w-xl',
        full: 'w-full max-w-none',
      },
      position: {
        center: 'left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2 rounded-xl',
        'mobile-bottom': 'left-0 right-0 bottom-0 rounded-t-xl sm:left-1/2 sm:top-1/2 sm:-translate-x-1/2 sm:-translate-y-1/2 sm:rounded-xl sm:bottom-auto',
      },
      animation: {
        scale: 'data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:zoom-out-95 data-[state=open]:zoom-in-95',
        slideUp: 'data-[state=open]:animate-in data-[state=closed]:animate-out data-[state=closed]:fade-out-0 data-[state=open]:fade-in-0 data-[state=closed]:slide-out-to-bottom data-[state=open]:slide-in-from-bottom sm:data-[state=closed]:zoom-out-95 sm:data-[state=open]:zoom-in-95',
        none: '',
      },
    },
    defaultVariants: {
      size: 'md',
      position: 'center',
      animation: 'scale',
    },
  }
)

interface DialogProps {
  open?: boolean
  onOpenChange?: (open: boolean) => void
  children: React.ReactNode
}

export function Dialog({ open, onOpenChange, children }: DialogProps) {
  return (
    <DialogPrimitive.Root open={open} onOpenChange={onOpenChange}>
      {children}
    </DialogPrimitive.Root>
  )
}

export const DialogTrigger = DialogPrimitive.Trigger

interface DialogContentProps
  extends React.ComponentPropsWithoutRef<typeof DialogPrimitive.Content>,
    VariantProps<typeof dialogContentVariants> {
  showCloseButton?: boolean
  overlayAnimation?: 'fade' | 'none'
}

export function DialogContent({
  children,
  size,
  position = 'mobile-bottom',
  animation = 'slideUp',
  showCloseButton = true,
  overlayAnimation = 'fade',
  className,
  ...props
}: DialogContentProps) {
  return (
    <DialogPrimitive.Portal>
      <DialogPrimitive.Overlay className={dialogOverlayVariants({ animation: overlayAnimation })} />
      <DialogPrimitive.Content
        className={dialogContentVariants({ size, position, animation, className })}
        {...props}
      >
        {children}
        {showCloseButton && (
          <DialogPrimitive.Close
            className="absolute right-4 top-4 p-2 rounded-full hover:bg-gray-100 transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2"
            aria-label="Close"
          >
            <X className="w-5 h-5 text-gray-500" />
          </DialogPrimitive.Close>
        )}
      </DialogPrimitive.Content>
    </DialogPrimitive.Portal>
  )
}

export function DialogHeader({
  children,
  className,
}: {
  children: React.ReactNode
  className?: string
}) {
  return (
    <div className={`px-6 pt-6 pb-4 ${className || ''}`}>
      {children}
    </div>
  )
}

export function DialogTitle({
  children,
  className,
}: {
  children: React.ReactNode
  className?: string
}) {
  return (
    <DialogPrimitive.Title
      className={`font-heading text-h3 text-gray-900 ${className || ''}`}
    >
      {children}
    </DialogPrimitive.Title>
  )
}

export function DialogDescription({
  children,
  className,
}: {
  children: React.ReactNode
  className?: string
}) {
  return (
    <DialogPrimitive.Description
      className={`font-body text-body text-gray-600 mt-2 ${className || ''}`}
    >
      {children}
    </DialogPrimitive.Description>
  )
}

export function DialogBody({
  children,
  className,
}: {
  children: React.ReactNode
  className?: string
}) {
  return (
    <div className={`px-6 py-4 ${className || ''}`}>
      {children}
    </div>
  )
}

export function DialogFooter({
  children,
  className,
}: {
  children: React.ReactNode
  className?: string
}) {
  return (
    <div className={`px-6 pb-6 pt-4 flex gap-3 ${className || ''}`}>
      {children}
    </div>
  )
}

export const DialogClose = DialogPrimitive.Close
```

---

## 1. Alert Dialogs

Alert dialogs require user acknowledgment before proceeding. Use for confirmations, warnings, and destructive actions.

### Alert Dialog Variants

| Variant | Actions | Layout | Use Case |
|---------|---------|--------|----------|
| **Single Action** | 1 button | Centered | Acknowledgments, info alerts |
| **Two Actions** | 2 buttons | Horizontal | Confirm/Cancel flows |
| **Two Actions Vertical** | 2 buttons | Stacked | Mobile-first, long button labels |

---

### Alert Dialog - Single Action

For acknowledgments that only require dismissal.

**When to use**: Information alerts, success confirmations, acknowledgment prompts

```tsx
interface AlertDialogProps {
  open: boolean
  onOpenChange: (open: boolean) => void
  title: string
  description: string
  actionLabel?: string
  onAction?: () => void
  variant?: 'info' | 'success' | 'warning' | 'error'
}

function AlertDialog({
  open,
  onOpenChange,
  title,
  description,
  actionLabel = 'Got it',
  onAction,
  variant = 'info',
}: AlertDialogProps) {
  const iconStyles = {
    info: 'bg-mereka-sky/30 text-mereka-blue',
    success: 'bg-mereka-mint/30 text-mereka-forest',
    warning: 'bg-mereka-gold/30 text-mereka-orange',
    error: 'bg-mereka-pink/30 text-mereka-burgundy',
  }

  const icons = {
    info: <InfoIcon className="w-6 h-6" />,
    success: <CheckCircleIcon className="w-6 h-6" />,
    warning: <AlertTriangleIcon className="w-6 h-6" />,
    error: <XCircleIcon className="w-6 h-6" />,
  }

  const handleAction = () => {
    onAction?.()
    onOpenChange(false)
  }

  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent size="sm" showCloseButton={false}>
        <div className="p-6 text-center">
          {/* Icon */}
          <div className={`w-14 h-14 rounded-full mx-auto mb-4 flex items-center justify-center ${iconStyles[variant]}`}>
            {icons[variant]}
          </div>

          {/* Content */}
          <DialogTitle className="text-center">{title}</DialogTitle>
          <DialogDescription className="text-center mt-2">
            {description}
          </DialogDescription>

          {/* Action */}
          <button
            onClick={handleAction}
            className="mt-6 w-full bg-gray-900 text-white rounded-full py-3 font-medium hover:bg-gray-800 transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2"
          >
            {actionLabel}
          </button>
        </div>
      </DialogContent>
    </Dialog>
  )
}
```

### Usage Example - Single Action

```tsx
// Success alert
<AlertDialog
  open={showSuccess}
  onOpenChange={setShowSuccess}
  variant="success"
  title="Booking Confirmed!"
  description="You've successfully booked Introduction to Pottery. Check your email for confirmation details."
  actionLabel="View Booking"
  onAction={() => navigate('/bookings')}
/>

// Info alert
<AlertDialog
  open={showInfo}
  onOpenChange={setShowInfo}
  variant="info"
  title="Session Reminder"
  description="Your pottery workshop starts in 1 hour. Don't forget to bring an apron!"
/>
```

---

### Alert Dialog - Two Actions (Horizontal)

For confirm/cancel flows with two clear options.

**When to use**: Confirmation dialogs, decision prompts, cancel confirmations

```tsx
interface ConfirmDialogProps {
  open: boolean
  onOpenChange: (open: boolean) => void
  title: string
  description: string
  confirmLabel?: string
  cancelLabel?: string
  onConfirm: () => void
  onCancel?: () => void
  variant?: 'default' | 'destructive'
  isLoading?: boolean
}

function ConfirmDialog({
  open,
  onOpenChange,
  title,
  description,
  confirmLabel = 'Confirm',
  cancelLabel = 'Cancel',
  onConfirm,
  onCancel,
  variant = 'default',
  isLoading = false,
}: ConfirmDialogProps) {
  const handleCancel = () => {
    onCancel?.()
    onOpenChange(false)
  }

  const handleConfirm = () => {
    onConfirm()
  }

  const confirmStyles = {
    default: 'bg-gray-900 text-white hover:bg-gray-800',
    destructive: 'bg-mereka-burgundy text-white hover:bg-mereka-burgundy/90',
  }

  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent size="sm" showCloseButton={false}>
        <DialogHeader>
          <DialogTitle>{title}</DialogTitle>
          <DialogDescription>{description}</DialogDescription>
        </DialogHeader>

        <DialogFooter className="justify-end">
          <button
            onClick={handleCancel}
            disabled={isLoading}
            className="flex-1 sm:flex-none border border-gray-300 text-gray-900 rounded-full px-5 py-2.5 font-medium hover:bg-gray-50 transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 disabled:opacity-50"
          >
            {cancelLabel}
          </button>
          <button
            onClick={handleConfirm}
            disabled={isLoading}
            className={`flex-1 sm:flex-none rounded-full px-5 py-2.5 font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 disabled:opacity-50 ${confirmStyles[variant]}`}
          >
            {isLoading ? (
              <span className="flex items-center gap-2">
                <LoadingSpinner className="w-4 h-4" />
                Processing...
              </span>
            ) : (
              confirmLabel
            )}
          </button>
        </DialogFooter>
      </DialogContent>
    </Dialog>
  )
}
```

### Usage Example - Cancel Booking

```tsx
function CancelBookingDialog({
  open,
  onOpenChange,
  booking,
  onCancel,
}: {
  open: boolean
  onOpenChange: (open: boolean) => void
  booking: Booking
  onCancel: () => Promise<void>
}) {
  const [isLoading, setIsLoading] = useState(false)

  const handleCancel = async () => {
    setIsLoading(true)
    try {
      await onCancel()
      onOpenChange(false)
    } finally {
      setIsLoading(false)
    }
  }

  return (
    <ConfirmDialog
      open={open}
      onOpenChange={onOpenChange}
      variant="destructive"
      title="Cancel Booking?"
      description={`Are you sure you want to cancel your booking for "${booking.experience.title}"? This action cannot be undone.`}
      confirmLabel="Yes, Cancel Booking"
      cancelLabel="Keep Booking"
      onConfirm={handleCancel}
      isLoading={isLoading}
    />
  )
}
```

---

### Alert Dialog - Two Actions (Vertical)

Stacked button layout for mobile-first designs or longer button labels.

**When to use**: Mobile dialogs, long action labels, emphasis on primary action

```tsx
interface VerticalConfirmDialogProps {
  open: boolean
  onOpenChange: (open: boolean) => void
  title: string
  description: string
  confirmLabel: string
  cancelLabel: string
  onConfirm: () => void
  onCancel?: () => void
  variant?: 'default' | 'destructive'
  icon?: React.ReactNode
  isLoading?: boolean
}

function VerticalConfirmDialog({
  open,
  onOpenChange,
  title,
  description,
  confirmLabel,
  cancelLabel,
  onConfirm,
  onCancel,
  variant = 'default',
  icon,
  isLoading = false,
}: VerticalConfirmDialogProps) {
  const handleCancel = () => {
    onCancel?.()
    onOpenChange(false)
  }

  const confirmStyles = {
    default: 'bg-gray-900 text-white hover:bg-gray-800',
    destructive: 'bg-mereka-burgundy text-white hover:bg-mereka-burgundy/90',
  }

  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent size="sm" showCloseButton={false}>
        <div className="p-6">
          {/* Icon */}
          {icon && (
            <div className="w-14 h-14 rounded-full mx-auto mb-4 flex items-center justify-center bg-mereka-pink/30 text-mereka-burgundy">
              {icon}
            </div>
          )}

          {/* Content */}
          <DialogTitle className={icon ? 'text-center' : ''}>
            {title}
          </DialogTitle>
          <DialogDescription className={`mt-2 ${icon ? 'text-center' : ''}`}>
            {description}
          </DialogDescription>

          {/* Stacked Actions */}
          <div className="mt-6 space-y-3">
            <button
              onClick={onConfirm}
              disabled={isLoading}
              className={`w-full rounded-full py-3 font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 disabled:opacity-50 ${confirmStyles[variant]}`}
            >
              {isLoading ? (
                <span className="flex items-center justify-center gap-2">
                  <LoadingSpinner className="w-4 h-4" />
                  Processing...
                </span>
              ) : (
                confirmLabel
              )}
            </button>
            <button
              onClick={handleCancel}
              disabled={isLoading}
              className="w-full border border-gray-300 text-gray-900 rounded-full py-3 font-medium hover:bg-gray-50 transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 disabled:opacity-50"
            >
              {cancelLabel}
            </button>
          </div>
        </div>
      </DialogContent>
    </Dialog>
  )
}
```

### Usage Example - Delete Experience

```tsx
<VerticalConfirmDialog
  open={showDeleteDialog}
  onOpenChange={setShowDeleteDialog}
  variant="destructive"
  icon={<TrashIcon className="w-6 h-6" />}
  title="Delete Experience?"
  description="This will permanently delete 'Introduction to Pottery' and all associated bookings. Attendees will be notified and refunded."
  confirmLabel="Delete Experience Permanently"
  cancelLabel="Keep Experience"
  onConfirm={handleDelete}
  isLoading={isDeleting}
/>
```

---

## 2. Form Dialogs

Form dialogs collect user input within a modal context.

### Form Dialog Structure

```tsx
interface FormDialogProps {
  open: boolean
  onOpenChange: (open: boolean) => void
  title: string
  description?: string
  children: React.ReactNode
  onSubmit: (e: React.FormEvent) => void
  submitLabel?: string
  cancelLabel?: string
  isSubmitting?: boolean
  size?: 'sm' | 'md' | 'lg' | 'xl'
}

function FormDialog({
  open,
  onOpenChange,
  title,
  description,
  children,
  onSubmit,
  submitLabel = 'Save',
  cancelLabel = 'Cancel',
  isSubmitting = false,
  size = 'md',
}: FormDialogProps) {
  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    onSubmit(e)
  }

  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent size={size}>
        <form onSubmit={handleSubmit}>
          <DialogHeader>
            <DialogTitle>{title}</DialogTitle>
            {description && (
              <DialogDescription>{description}</DialogDescription>
            )}
          </DialogHeader>

          <DialogBody className="max-h-[60vh] overflow-y-auto">
            {children}
          </DialogBody>

          <DialogFooter className="border-t border-gray-100 bg-gray-50 rounded-b-xl">
            <DialogClose asChild>
              <button
                type="button"
                disabled={isSubmitting}
                className="flex-1 sm:flex-none border border-gray-300 text-gray-900 rounded-full px-5 py-2.5 font-medium hover:bg-white transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 disabled:opacity-50"
              >
                {cancelLabel}
              </button>
            </DialogClose>
            <button
              type="submit"
              disabled={isSubmitting}
              className="flex-1 sm:flex-none bg-mereka-teal text-white rounded-full px-5 py-2.5 font-medium hover:bg-mereka-teal/90 transition-colors focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 disabled:opacity-50"
            >
              {isSubmitting ? (
                <span className="flex items-center gap-2">
                  <LoadingSpinner className="w-4 h-4" />
                  Saving...
                </span>
              ) : (
                submitLabel
              )}
            </button>
          </DialogFooter>
        </form>
      </DialogContent>
    </Dialog>
  )
}
```

---

### Add Team Member Dialog

**When to use**: Inviting new members to a hub or team

```tsx
interface TeamMember {
  email: string
  role: 'admin' | 'editor' | 'viewer'
  name?: string
}

function AddTeamMemberDialog({
  open,
  onOpenChange,
  onAdd,
}: {
  open: boolean
  onOpenChange: (open: boolean) => void
  onAdd: (member: TeamMember) => Promise<void>
}) {
  const [email, setEmail] = useState('')
  const [name, setName] = useState('')
  const [role, setRole] = useState<TeamMember['role']>('editor')
  const [isSubmitting, setIsSubmitting] = useState(false)
  const [error, setError] = useState('')

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    setError('')

    if (!email) {
      setError('Email is required')
      return
    }

    setIsSubmitting(true)
    try {
      await onAdd({ email, name, role })
      onOpenChange(false)
      // Reset form
      setEmail('')
      setName('')
      setRole('editor')
    } catch (err) {
      setError(err instanceof Error ? err.message : 'Failed to add member')
    } finally {
      setIsSubmitting(false)
    }
  }

  return (
    <FormDialog
      open={open}
      onOpenChange={onOpenChange}
      title="Add Team Member"
      description="Invite a new member to your hub. They'll receive an email invitation."
      onSubmit={handleSubmit}
      submitLabel="Send Invitation"
      isSubmitting={isSubmitting}
    >
      <div className="space-y-4">
        {/* Email Field */}
        <div className="space-y-1.5">
          <label className="block font-body text-body-sm font-medium text-gray-700">
            Email Address <span className="text-red-500">*</span>
          </label>
          <input
            type="email"
            value={email}
            onChange={(e) => setEmail(e.target.value)}
            placeholder="colleague@example.com"
            className="w-full h-11 px-4 rounded-lg border border-gray-300 font-body text-body placeholder:text-gray-400 focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal"
            required
          />
        </div>

        {/* Name Field (Optional) */}
        <div className="space-y-1.5">
          <label className="block font-body text-body-sm font-medium text-gray-700">
            Name <span className="text-gray-400">(optional)</span>
          </label>
          <input
            type="text"
            value={name}
            onChange={(e) => setName(e.target.value)}
            placeholder="Their name"
            className="w-full h-11 px-4 rounded-lg border border-gray-300 font-body text-body placeholder:text-gray-400 focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal"
          />
        </div>

        {/* Role Selection */}
        <div className="space-y-1.5">
          <label className="block font-body text-body-sm font-medium text-gray-700">
            Role
          </label>
          <div className="space-y-2">
            {[
              {
                value: 'admin',
                label: 'Admin',
                description: 'Full access to manage hub settings, team, and experiences',
              },
              {
                value: 'editor',
                label: 'Editor',
                description: 'Can create and edit experiences, view bookings',
              },
              {
                value: 'viewer',
                label: 'Viewer',
                description: 'Can only view experiences and bookings',
              },
            ].map((option) => (
              <label
                key={option.value}
                className={`flex items-start gap-3 p-3 rounded-lg border cursor-pointer transition-colors ${
                  role === option.value
                    ? 'border-mereka-teal bg-mereka-teal/5'
                    : 'border-gray-200 hover:border-gray-300'
                }`}
              >
                <input
                  type="radio"
                  name="role"
                  value={option.value}
                  checked={role === option.value}
                  onChange={(e) => setRole(e.target.value as TeamMember['role'])}
                  className="mt-1 w-4 h-4 text-mereka-teal focus:ring-mereka-teal"
                />
                <div>
                  <span className="font-medium text-gray-900">{option.label}</span>
                  <p className="text-body-sm text-gray-500">{option.description}</p>
                </div>
              </label>
            ))}
          </div>
        </div>

        {/* Error Message */}
        {error && (
          <div className="flex items-center gap-2 p-3 rounded-lg bg-red-50 text-red-700">
            <AlertCircleIcon className="w-4 h-4 flex-shrink-0" />
            <p className="text-body-sm">{error}</p>
          </div>
        )}
      </div>
    </FormDialog>
  )
}
```

### Usage Example

```tsx
function TeamSettings() {
  const [showAddMember, setShowAddMember] = useState(false)

  const handleAddMember = async (member: TeamMember) => {
    await api.inviteTeamMember(member)
    toast.success('Invitation sent!')
  }

  return (
    <>
      <button
        onClick={() => setShowAddMember(true)}
        className="bg-mereka-teal text-white rounded-full px-4 py-2 font-medium"
      >
        Add Team Member
      </button>

      <AddTeamMemberDialog
        open={showAddMember}
        onOpenChange={setShowAddMember}
        onAdd={handleAddMember}
      />
    </>
  )
}
```

---

### Edit Profile Dialog

**When to use**: Quick profile edits without leaving current page

```tsx
interface ProfileData {
  name: string
  bio: string
  avatar?: File | null
  avatarUrl?: string
}

function EditProfileDialog({
  open,
  onOpenChange,
  initialData,
  onSave,
}: {
  open: boolean
  onOpenChange: (open: boolean) => void
  initialData: ProfileData
  onSave: (data: ProfileData) => Promise<void>
}) {
  const [formData, setFormData] = useState(initialData)
  const [isSubmitting, setIsSubmitting] = useState(false)
  const [avatarPreview, setAvatarPreview] = useState<string | null>(initialData.avatarUrl || null)

  const handleAvatarChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0]
    if (file) {
      setFormData({ ...formData, avatar: file })
      setAvatarPreview(URL.createObjectURL(file))
    }
  }

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    setIsSubmitting(true)
    try {
      await onSave(formData)
      onOpenChange(false)
    } finally {
      setIsSubmitting(false)
    }
  }

  return (
    <FormDialog
      open={open}
      onOpenChange={onOpenChange}
      title="Edit Profile"
      onSubmit={handleSubmit}
      submitLabel="Save Changes"
      isSubmitting={isSubmitting}
    >
      <div className="space-y-6">
        {/* Avatar Upload */}
        <div className="flex items-center gap-4">
          <div className="relative">
            {avatarPreview ? (
              <img
                src={avatarPreview}
                alt="Avatar preview"
                className="w-20 h-20 rounded-full object-cover"
              />
            ) : (
              <div className="w-20 h-20 rounded-full bg-gray-200 flex items-center justify-center">
                <UserIcon className="w-8 h-8 text-gray-400" />
              </div>
            )}
            <label className="absolute -bottom-1 -right-1 w-8 h-8 bg-gray-900 rounded-full flex items-center justify-center cursor-pointer hover:bg-gray-800 transition-colors">
              <CameraIcon className="w-4 h-4 text-white" />
              <input
                type="file"
                accept="image/*"
                onChange={handleAvatarChange}
                className="sr-only"
              />
            </label>
          </div>
          <div>
            <p className="text-body-sm font-medium text-gray-900">Profile Photo</p>
            <p className="text-caption text-gray-500">JPG or PNG. Max 5MB.</p>
          </div>
        </div>

        {/* Name Field */}
        <div className="space-y-1.5">
          <label className="block font-body text-body-sm font-medium text-gray-700">
            Display Name
          </label>
          <input
            type="text"
            value={formData.name}
            onChange={(e) => setFormData({ ...formData, name: e.target.value })}
            className="w-full h-11 px-4 rounded-lg border border-gray-300 font-body text-body focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal"
          />
        </div>

        {/* Bio Field */}
        <div className="space-y-1.5">
          <label className="block font-body text-body-sm font-medium text-gray-700">
            Bio
          </label>
          <textarea
            value={formData.bio}
            onChange={(e) => setFormData({ ...formData, bio: e.target.value })}
            rows={4}
            maxLength={500}
            className="w-full px-4 py-3 rounded-lg border border-gray-300 font-body text-body resize-none focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal"
            placeholder="Tell others about yourself..."
          />
          <p className="text-caption text-gray-400 text-right">
            {formData.bio.length}/500
          </p>
        </div>
      </div>
    </FormDialog>
  )
}
```

---

## 3. Content Dialogs

Content dialogs display information without requiring user input.

### Image Preview Dialog

**When to use**: Full-size image viewing, gallery previews

```tsx
interface ImagePreviewDialogProps {
  open: boolean
  onOpenChange: (open: boolean) => void
  images: Array<{
    src: string
    alt: string
  }>
  initialIndex?: number
}

function ImagePreviewDialog({
  open,
  onOpenChange,
  images,
  initialIndex = 0,
}: ImagePreviewDialogProps) {
  const [currentIndex, setCurrentIndex] = useState(initialIndex)

  const goToPrevious = () => {
    setCurrentIndex((prev) => (prev === 0 ? images.length - 1 : prev - 1))
  }

  const goToNext = () => {
    setCurrentIndex((prev) => (prev === images.length - 1 ? 0 : prev + 1))
  }

  // Handle keyboard navigation
  useEffect(() => {
    const handleKeyDown = (e: KeyboardEvent) => {
      if (!open) return
      if (e.key === 'ArrowLeft') goToPrevious()
      if (e.key === 'ArrowRight') goToNext()
    }
    window.addEventListener('keydown', handleKeyDown)
    return () => window.removeEventListener('keydown', handleKeyDown)
  }, [open])

  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent
        size="full"
        position="center"
        className="bg-black/95 max-w-none w-screen h-screen rounded-none"
      >
        {/* Close button */}
        <button
          onClick={() => onOpenChange(false)}
          className="absolute top-4 right-4 p-2 rounded-full bg-white/10 hover:bg-white/20 transition-colors z-10"
          aria-label="Close"
        >
          <X className="w-6 h-6 text-white" />
        </button>

        {/* Image counter */}
        <div className="absolute top-4 left-4 text-white/80 text-body-sm z-10">
          {currentIndex + 1} / {images.length}
        </div>

        {/* Main image */}
        <div className="flex items-center justify-center h-full px-16">
          <img
            src={images[currentIndex].src}
            alt={images[currentIndex].alt}
            className="max-w-full max-h-full object-contain"
          />
        </div>

        {/* Navigation arrows */}
        {images.length > 1 && (
          <>
            <button
              onClick={goToPrevious}
              className="absolute left-4 top-1/2 -translate-y-1/2 p-3 rounded-full bg-white/10 hover:bg-white/20 transition-colors"
              aria-label="Previous image"
            >
              <ChevronLeftIcon className="w-6 h-6 text-white" />
            </button>
            <button
              onClick={goToNext}
              className="absolute right-4 top-1/2 -translate-y-1/2 p-3 rounded-full bg-white/10 hover:bg-white/20 transition-colors"
              aria-label="Next image"
            >
              <ChevronRightIcon className="w-6 h-6 text-white" />
            </button>
          </>
        )}

        {/* Thumbnail strip */}
        {images.length > 1 && (
          <div className="absolute bottom-4 left-1/2 -translate-x-1/2 flex gap-2 p-2 bg-black/50 rounded-lg">
            {images.map((image, index) => (
              <button
                key={index}
                onClick={() => setCurrentIndex(index)}
                className={`w-12 h-12 rounded overflow-hidden transition-opacity ${
                  index === currentIndex ? 'ring-2 ring-white' : 'opacity-60 hover:opacity-100'
                }`}
              >
                <img
                  src={image.src}
                  alt=""
                  className="w-full h-full object-cover"
                />
              </button>
            ))}
          </div>
        )}
      </DialogContent>
    </Dialog>
  )
}
```

---

### Detail Preview Dialog

**When to use**: Quick previews, experience details, content previews

```tsx
interface ExperiencePreviewDialogProps {
  open: boolean
  onOpenChange: (open: boolean) => void
  experience: {
    id: string
    title: string
    description: string
    coverImage: string
    price: number
    currency: string
    duration: string
    host: {
      name: string
      avatar: string
    }
    rating?: number
    reviewCount?: number
  }
  onBook?: () => void
}

function ExperiencePreviewDialog({
  open,
  onOpenChange,
  experience,
  onBook,
}: ExperiencePreviewDialogProps) {
  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent size="lg">
        {/* Cover Image */}
        <div className="aspect-video relative overflow-hidden rounded-t-xl">
          <img
            src={experience.coverImage}
            alt={experience.title}
            className="w-full h-full object-cover"
          />
        </div>

        <div className="p-6">
          {/* Host */}
          <div className="flex items-center gap-2 mb-3">
            <img
              src={experience.host.avatar}
              alt={experience.host.name}
              className="w-8 h-8 rounded-full object-cover"
            />
            <span className="text-body-sm text-gray-600">{experience.host.name}</span>
          </div>

          {/* Title */}
          <DialogTitle className="text-h2">{experience.title}</DialogTitle>

          {/* Rating */}
          {experience.rating && (
            <div className="flex items-center gap-1 mt-2">
              <StarIcon className="w-4 h-4 text-mereka-gold fill-mereka-gold" />
              <span className="text-body-sm font-medium">{experience.rating.toFixed(1)}</span>
              {experience.reviewCount && (
                <span className="text-caption text-gray-400">({experience.reviewCount} reviews)</span>
              )}
            </div>
          )}

          {/* Description */}
          <p className="text-body text-gray-600 mt-4 line-clamp-4">
            {experience.description}
          </p>

          {/* Meta */}
          <div className="flex items-center gap-4 mt-4 text-body-sm text-gray-500">
            <span className="flex items-center gap-1">
              <ClockIcon className="w-4 h-4" />
              {experience.duration}
            </span>
          </div>

          {/* Footer */}
          <div className="flex items-center justify-between mt-6 pt-6 border-t border-gray-100">
            <div>
              <span className="text-caption text-gray-400">From</span>
              <p className="font-heading text-h3 text-gray-900">
                {experience.currency} {experience.price.toLocaleString()}
              </p>
            </div>

            <div className="flex gap-3">
              <DialogClose asChild>
                <button className="border border-gray-300 text-gray-900 rounded-full px-5 py-2.5 font-medium hover:bg-gray-50 transition-colors">
                  Close
                </button>
              </DialogClose>
              {onBook && (
                <button
                  onClick={onBook}
                  className="bg-mereka-teal text-white rounded-full px-5 py-2.5 font-medium hover:bg-mereka-teal/90 transition-colors"
                >
                  Book Now
                </button>
              )}
            </div>
          </div>
        </div>
      </DialogContent>
    </Dialog>
  )
}
```

---

## Backdrop & Overlay Styling

### Default Overlay

```tsx
// Standard semi-transparent backdrop
<DialogPrimitive.Overlay className="fixed inset-0 z-50 bg-black/50 backdrop-blur-sm" />
```

### Overlay Variants

| Variant | Style | Use Case |
|---------|-------|----------|
| **Standard** | `bg-black/50` | General dialogs |
| **Dark** | `bg-black/80` | Image previews, video |
| **Light** | `bg-black/30` | Less intrusive overlays |
| **Blur** | `backdrop-blur-sm` | Modern, glass effect |

```tsx
const overlayVariants = cva('fixed inset-0 z-50', {
  variants: {
    intensity: {
      light: 'bg-black/30',
      standard: 'bg-black/50',
      dark: 'bg-black/80',
    },
    blur: {
      none: '',
      sm: 'backdrop-blur-sm',
      md: 'backdrop-blur-md',
    },
  },
  defaultVariants: {
    intensity: 'standard',
    blur: 'sm',
  },
})
```

---

## Animation

### Animation Keyframes

Add these to your Tailwind config for dialog animations:

```typescript
// tailwind.config.ts
module.exports = {
  theme: {
    extend: {
      keyframes: {
        'fade-in': {
          from: { opacity: '0' },
          to: { opacity: '1' },
        },
        'fade-out': {
          from: { opacity: '1' },
          to: { opacity: '0' },
        },
        'zoom-in': {
          from: { opacity: '0', transform: 'translate(-50%, -50%) scale(0.95)' },
          to: { opacity: '1', transform: 'translate(-50%, -50%) scale(1)' },
        },
        'zoom-out': {
          from: { opacity: '1', transform: 'translate(-50%, -50%) scale(1)' },
          to: { opacity: '0', transform: 'translate(-50%, -50%) scale(0.95)' },
        },
        'slide-in-from-bottom': {
          from: { opacity: '0', transform: 'translateY(100%)' },
          to: { opacity: '1', transform: 'translateY(0)' },
        },
        'slide-out-to-bottom': {
          from: { opacity: '1', transform: 'translateY(0)' },
          to: { opacity: '0', transform: 'translateY(100%)' },
        },
      },
      animation: {
        'fade-in': 'fade-in 200ms ease-out',
        'fade-out': 'fade-out 150ms ease-in',
        'zoom-in': 'zoom-in 200ms ease-out',
        'zoom-out': 'zoom-out 150ms ease-in',
        'slide-in-from-bottom': 'slide-in-from-bottom 300ms ease-out',
        'slide-out-to-bottom': 'slide-out-to-bottom 200ms ease-in',
      },
    },
  },
}
```

### Animation Durations

| Animation | Duration | Easing | Use Case |
|-----------|----------|--------|----------|
| **Fade in** | 200ms | ease-out | Overlay appearance |
| **Fade out** | 150ms | ease-in | Overlay dismissal |
| **Scale in** | 200ms | ease-out | Dialog entrance |
| **Scale out** | 150ms | ease-in | Dialog dismissal |
| **Slide in** | 300ms | ease-out | Mobile bottom sheet |
| **Slide out** | 200ms | ease-in | Mobile bottom sheet |

---

## Accessibility

### Keyboard Navigation

| Key | Action |
|-----|--------|
| **Escape** | Close dialog |
| **Tab** | Move focus to next focusable element |
| **Shift + Tab** | Move focus to previous element |
| **Enter/Space** | Activate focused button |

### Focus Management

```tsx
// Radix UI handles focus trapping automatically
// First focusable element receives focus on open
// Focus returns to trigger on close

// For custom focus on open:
<DialogContent
  onOpenAutoFocus={(e) => {
    e.preventDefault()
    // Focus specific element
    document.getElementById('email-input')?.focus()
  }}
>
```

### Required ARIA Attributes

Radix UI Dialog provides these automatically:

```tsx
// On dialog trigger
role="button"
aria-haspopup="dialog"
aria-expanded="true|false"

// On dialog content
role="dialog"
aria-modal="true"
aria-labelledby="dialog-title"
aria-describedby="dialog-description"
```

### Screen Reader Announcements

```tsx
<DialogContent
  aria-label="Add team member dialog"
  aria-describedby="add-member-description"
>
  <DialogTitle id="dialog-title">Add Team Member</DialogTitle>
  <DialogDescription id="add-member-description">
    Invite a new member to your hub.
  </DialogDescription>
</DialogContent>
```

### Focus Trap Implementation

Radix UI Dialog includes focus trapping by default. For custom implementations:

```tsx
import { FocusTrap } from '@radix-ui/react-focus-scope'

function CustomDialog({ children, ...props }) {
  return (
    <FocusTrap asChild loop>
      <div role="dialog" aria-modal="true" {...props}>
        {children}
      </div>
    </FocusTrap>
  )
}
```

---

## Mobile Behavior

### Full-Screen on Small Screens

Dialogs should adapt to mobile viewports:

```tsx
const dialogContentVariants = cva(
  'fixed z-50 bg-white shadow-xl focus:outline-none',
  {
    variants: {
      // ... other variants
      responsive: {
        // Full screen on mobile, centered on desktop
        adaptive: `
          inset-0 rounded-none
          sm:inset-auto sm:left-1/2 sm:top-1/2
          sm:-translate-x-1/2 sm:-translate-y-1/2
          sm:rounded-xl sm:max-w-md
        `,
        // Bottom sheet on mobile, centered on desktop
        bottomSheet: `
          left-0 right-0 bottom-0 rounded-t-xl max-h-[90vh]
          sm:left-1/2 sm:top-1/2 sm:bottom-auto
          sm:-translate-x-1/2 sm:-translate-y-1/2
          sm:rounded-xl sm:max-w-md
        `,
      },
    },
  }
)
```

### Mobile Bottom Sheet Pattern

```tsx
function MobileOptimizedDialog({
  open,
  onOpenChange,
  children,
}: {
  open: boolean
  onOpenChange: (open: boolean) => void
  children: React.ReactNode
}) {
  return (
    <Dialog open={open} onOpenChange={onOpenChange}>
      <DialogContent
        position="mobile-bottom"
        animation="slideUp"
        className="max-h-[90vh] overflow-hidden"
      >
        {/* Drag handle for mobile */}
        <div className="flex justify-center py-2 sm:hidden">
          <div className="w-12 h-1 bg-gray-300 rounded-full" />
        </div>

        {children}
      </DialogContent>
    </Dialog>
  )
}
```

### Safe Area Handling

```tsx
// For devices with notches or home indicators
<DialogContent className="pb-safe">
  {/* Content */}
</DialogContent>

// In tailwind.config.ts
module.exports = {
  theme: {
    extend: {
      padding: {
        safe: 'env(safe-area-inset-bottom)',
      },
    },
  },
}
```

---

## Dialog Sizes

| Size | Max Width | Use Case |
|------|-----------|----------|
| **sm** | 384px | Alerts, confirmations |
| **md** | 448px | Forms, standard dialogs |
| **lg** | 512px | Complex forms, previews |
| **xl** | 576px | Multi-step forms |
| **full** | 100% | Image previews, full-screen |

---

## Do's and Don'ts

### Do's

- **Do** use Alert Dialogs for destructive actions requiring confirmation
- **Do** provide clear action labels ("Delete Experience" not "Yes")
- **Do** maintain focus within the dialog
- **Do** allow Escape key to close non-critical dialogs
- **Do** use appropriate dialog size for content
- **Do** animate dialog entrance and exit
- **Do** provide loading states for async actions
- **Do** use bottom sheet pattern on mobile
- **Do** include a clear way to dismiss (close button or cancel)

### Don'ts

- **Don't** nest dialogs (open dialog from dialog)
- **Don't** use dialogs for simple notifications (use toasts instead)
- **Don't** make dialogs too large or scrollable when possible
- **Don't** auto-focus destructive action buttons
- **Don't** prevent Escape from closing confirmation dialogs
- **Don't** use generic labels like "OK" and "Cancel"
- **Don't** skip the backdrop (overlay) unless intentional
- **Don't** leave scroll locked after dialog closes
- **Don't** forget to handle form validation before closing

---

## Related Documentation

- [Buttons](./buttons.md) - Dialog action button styles
- [Inputs](./inputs.md) - Form dialog input components
- [Colors](./colors.md) - Status and action colors
- [Typography](./typography.md) - Dialog text styles
- [Patterns](./patterns.md) - Common dialog use cases
