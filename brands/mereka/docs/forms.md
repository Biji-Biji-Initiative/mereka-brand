# Forms

> Comprehensive guide to form patterns in Mereka. Covers layouts, validation, multi-step workflows, and complete form implementations using react-hook-form and Zod.

**Figma Sections**: Inputs, Dialogs, Patterns

---

## Form Architecture Overview

```
Form Container
├── Form Header (optional)
│   ├── Title
│   └── Description
├── Form Sections
│   ├── Section Header (optional)
│   ├── Field Groups
│   │   ├── Label + Required Indicator
│   │   ├── Input Component
│   │   ├── Helper Text (optional)
│   │   └── Error Message (conditional)
│   └── Section Divider (optional)
├── Form Actions
│   ├── Secondary Actions (Cancel, Save Draft)
│   └── Primary Action (Submit)
└── Form Feedback (Success/Error States)
```

---

## Quick Reference

| Pattern | Usage | Complexity |
|---------|-------|------------|
| **Single Column** | Simple forms, mobile | Low |
| **Two Column** | Address, profile | Medium |
| **Sectioned** | Settings, preferences | Medium |
| **Multi-Step** | Onboarding, creation flows | High |

---

## 1. Form Layouts

### Single Column Layout

The default layout for most forms. Best for mobile and simple data collection.

```tsx
interface SingleColumnFormProps {
  title?: string
  description?: string
  children: React.ReactNode
  onSubmit: (e: React.FormEvent) => void
}

function SingleColumnForm({
  title,
  description,
  children,
  onSubmit,
}: SingleColumnFormProps) {
  return (
    <form onSubmit={onSubmit} className="max-w-lg mx-auto">
      {(title || description) && (
        <div className="mb-8">
          {title && (
            <h1 className="font-heading text-h2 text-gray-900">{title}</h1>
          )}
          {description && (
            <p className="text-body text-gray-600 mt-2">{description}</p>
          )}
        </div>
      )}

      <div className="space-y-6">{children}</div>
    </form>
  )
}
```

#### Usage

```tsx
<SingleColumnForm
  title="Contact Us"
  description="We'd love to hear from you."
  onSubmit={handleSubmit}
>
  <Input label="Full Name" {...register('name')} />
  <Input label="Email" type="email" {...register('email')} />
  <Textarea label="Message" {...register('message')} />
  <Button type="submit" variant="solid">Send Message</Button>
</SingleColumnForm>
```

---

### Two Column Layout

For forms with related fields that can be grouped horizontally. Collapses to single column on mobile.

```tsx
interface TwoColumnFormProps {
  title?: string
  description?: string
  children: React.ReactNode
  onSubmit: (e: React.FormEvent) => void
}

function TwoColumnForm({
  title,
  description,
  children,
  onSubmit,
}: TwoColumnFormProps) {
  return (
    <form onSubmit={onSubmit} className="max-w-2xl mx-auto">
      {(title || description) && (
        <div className="mb-8">
          {title && (
            <h1 className="font-heading text-h2 text-gray-900">{title}</h1>
          )}
          {description && (
            <p className="text-body text-gray-600 mt-2">{description}</p>
          )}
        </div>
      )}

      {children}
    </form>
  )
}

// Field row component for two column layouts
interface FormRowProps {
  children: React.ReactNode
  columns?: 1 | 2 | 3
}

function FormRow({ children, columns = 2 }: FormRowProps) {
  const gridCols = {
    1: 'grid-cols-1',
    2: 'grid-cols-1 md:grid-cols-2',
    3: 'grid-cols-1 md:grid-cols-3',
  }

  return (
    <div className={`grid ${gridCols[columns]} gap-4 mb-6`}>
      {children}
    </div>
  )
}
```

#### Usage

```tsx
<TwoColumnForm
  title="Shipping Address"
  description="Enter your delivery details."
  onSubmit={handleSubmit}
>
  <FormRow>
    <Input label="First Name" {...register('firstName')} />
    <Input label="Last Name" {...register('lastName')} />
  </FormRow>

  <FormRow columns={1}>
    <Input label="Street Address" {...register('street')} />
  </FormRow>

  <FormRow>
    <Input label="City" {...register('city')} />
    <Input label="State/Province" {...register('state')} />
  </FormRow>

  <FormRow columns={3}>
    <Select label="Country" options={countries} {...register('country')} />
    <Input label="Postal Code" {...register('postalCode')} />
    <Input label="Phone" type="tel" {...register('phone')} />
  </FormRow>

  <Button type="submit" variant="solid">Save Address</Button>
</TwoColumnForm>
```

---

### Sectioned Form Layout

For complex forms with distinct logical groupings. Uses visual dividers and section headers.

```tsx
interface FormSectionProps {
  title: string
  description?: string
  children: React.ReactNode
}

function FormSection({ title, description, children }: FormSectionProps) {
  return (
    <section className="py-8 first:pt-0">
      <div className="mb-6">
        <h2 className="font-heading text-h4 text-gray-900">{title}</h2>
        {description && (
          <p className="text-body-sm text-gray-500 mt-1">{description}</p>
        )}
      </div>
      <div className="space-y-4">{children}</div>
    </section>
  )
}

interface SectionedFormProps {
  title?: string
  description?: string
  children: React.ReactNode
  onSubmit: (e: React.FormEvent) => void
}

function SectionedForm({
  title,
  description,
  children,
  onSubmit,
}: SectionedFormProps) {
  return (
    <form onSubmit={onSubmit} className="max-w-2xl mx-auto">
      {(title || description) && (
        <div className="mb-8 pb-8 border-b border-gray-200">
          {title && (
            <h1 className="font-heading text-h2 text-gray-900">{title}</h1>
          )}
          {description && (
            <p className="text-body text-gray-600 mt-2">{description}</p>
          )}
        </div>
      )}

      <div className="divide-y divide-gray-200">{children}</div>
    </form>
  )
}
```

#### Usage

```tsx
<SectionedForm
  title="Account Settings"
  description="Manage your account preferences."
  onSubmit={handleSubmit}
>
  <FormSection
    title="Personal Information"
    description="Update your personal details."
  >
    <FormRow>
      <Input label="Full Name" {...register('name')} />
      <Input label="Email" type="email" {...register('email')} />
    </FormRow>
    <Input label="Bio" {...register('bio')} />
  </FormSection>

  <FormSection
    title="Notifications"
    description="Choose how you want to be notified."
  >
    <Toggle
      label="Email notifications"
      description="Receive booking confirmations and updates"
      {...register('emailNotifications')}
    />
    <Toggle
      label="Push notifications"
      description="Get instant alerts on your device"
      {...register('pushNotifications')}
    />
  </FormSection>

  <FormSection title="Privacy">
    <Checkbox
      label="Make profile public"
      description="Allow others to see your profile and reviews"
      {...register('publicProfile')}
    />
  </FormSection>

  <div className="pt-8 flex justify-end gap-3">
    <Button variant="outline">Cancel</Button>
    <Button type="submit" variant="solid">Save Changes</Button>
  </div>
</SectionedForm>
```

---

## 2. Multi-Step Forms

Multi-step forms break complex workflows into manageable steps. Essential for experience creation, booking flows, and onboarding.

### Step Progress Indicator

```tsx
interface Step {
  id: string
  title: string
  description?: string
}

interface StepIndicatorProps {
  steps: Step[]
  currentStep: number
  onStepClick?: (stepIndex: number) => void
  allowNavigation?: boolean
}

function StepIndicator({
  steps,
  currentStep,
  onStepClick,
  allowNavigation = false,
}: StepIndicatorProps) {
  return (
    <nav aria-label="Progress">
      <ol className="flex items-center">
        {steps.map((step, index) => {
          const isCompleted = index < currentStep
          const isCurrent = index === currentStep
          const isClickable = allowNavigation && index <= currentStep

          return (
            <li key={step.id} className="flex items-center">
              {/* Step circle */}
              <button
                type="button"
                disabled={!isClickable}
                onClick={() => isClickable && onStepClick?.(index)}
                className={`
                  w-10 h-10 rounded-full flex items-center justify-center font-medium text-body-sm
                  transition-colors
                  ${isCompleted
                    ? 'bg-mereka-teal text-white'
                    : isCurrent
                    ? 'bg-gray-900 text-white'
                    : 'bg-gray-200 text-gray-500'
                  }
                  ${isClickable ? 'cursor-pointer hover:opacity-80' : 'cursor-default'}
                `}
                aria-current={isCurrent ? 'step' : undefined}
              >
                {isCompleted ? (
                  <CheckIcon className="w-5 h-5" />
                ) : (
                  index + 1
                )}
              </button>

              {/* Step label (visible on larger screens) */}
              <span className="hidden sm:block ml-3 mr-8 text-body-sm">
                <span className={`block font-medium ${isCurrent ? 'text-gray-900' : 'text-gray-500'}`}>
                  {step.title}
                </span>
                {step.description && (
                  <span className="text-gray-400">{step.description}</span>
                )}
              </span>

              {/* Connector line */}
              {index < steps.length - 1 && (
                <div className={`
                  w-12 sm:w-20 h-0.5 mx-2 sm:mx-0
                  ${isCompleted ? 'bg-mereka-teal' : 'bg-gray-200'}
                `} />
              )}
            </li>
          )
        })}
      </ol>
    </nav>
  )
}
```

### Multi-Step Form Container

```tsx
interface MultiStepFormProps<T extends FieldValues> {
  steps: Step[]
  currentStep: number
  onNext: () => void
  onBack: () => void
  onSubmit: (data: T) => void
  isSubmitting?: boolean
  children: React.ReactNode
}

function MultiStepForm<T extends FieldValues>({
  steps,
  currentStep,
  onNext,
  onBack,
  onSubmit,
  isSubmitting = false,
  children,
}: MultiStepFormProps<T>) {
  const isFirstStep = currentStep === 0
  const isLastStep = currentStep === steps.length - 1

  return (
    <div className="min-h-screen flex flex-col">
      {/* Header with progress */}
      <header className="sticky top-0 z-10 bg-white border-b border-gray-200">
        <div className="container mx-auto px-4 py-4">
          <StepIndicator
            steps={steps}
            currentStep={currentStep}
            allowNavigation={true}
          />
        </div>
      </header>

      {/* Step content */}
      <main className="flex-1 py-8">
        <div className="container mx-auto px-4 max-w-2xl">
          <div className="mb-8">
            <h1 className="font-heading text-h1 text-gray-900">
              {steps[currentStep].title}
            </h1>
            {steps[currentStep].description && (
              <p className="text-body-lg text-gray-600 mt-2">
                {steps[currentStep].description}
              </p>
            )}
          </div>

          {children}
        </div>
      </main>

      {/* Footer with navigation */}
      <footer className="sticky bottom-0 bg-white border-t border-gray-200">
        <div className="container mx-auto px-4 py-4">
          <div className="flex justify-between max-w-2xl mx-auto">
            <Button
              type="button"
              variant="ghost"
              onClick={onBack}
              disabled={isFirstStep || isSubmitting}
            >
              <ChevronLeftIcon className="w-4 h-4 mr-1" />
              Back
            </Button>

            {isLastStep ? (
              <Button
                type="submit"
                variant="solid"
                disabled={isSubmitting}
              >
                {isSubmitting ? (
                  <>
                    <LoaderIcon className="w-4 h-4 mr-2 animate-spin" />
                    Submitting...
                  </>
                ) : (
                  'Submit'
                )}
              </Button>
            ) : (
              <Button
                type="button"
                variant="solid"
                onClick={onNext}
              >
                Continue
                <ChevronRightIcon className="w-4 h-4 ml-1" />
              </Button>
            )}
          </div>
        </div>
      </footer>
    </div>
  )
}
```

### Step State Management Hook

```tsx
import { useState, useCallback } from 'react'
import { useForm, UseFormReturn } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

interface UseMultiStepFormOptions<T extends z.ZodType> {
  schemas: T[]
  defaultValues: z.infer<T>
  onComplete: (data: z.infer<T>) => Promise<void>
}

interface UseMultiStepFormReturn<T extends z.ZodType> {
  currentStep: number
  isFirstStep: boolean
  isLastStep: boolean
  isSubmitting: boolean
  form: UseFormReturn<z.infer<T>>
  next: () => Promise<boolean>
  back: () => void
  goToStep: (step: number) => void
  submit: () => Promise<void>
}

function useMultiStepForm<T extends z.ZodType>({
  schemas,
  defaultValues,
  onComplete,
}: UseMultiStepFormOptions<T>): UseMultiStepFormReturn<T> {
  const [currentStep, setCurrentStep] = useState(0)
  const [isSubmitting, setIsSubmitting] = useState(false)

  const form = useForm({
    resolver: zodResolver(schemas[currentStep]),
    defaultValues,
    mode: 'onBlur',
  })

  const isFirstStep = currentStep === 0
  const isLastStep = currentStep === schemas.length - 1

  const next = useCallback(async () => {
    const isValid = await form.trigger()
    if (isValid && !isLastStep) {
      setCurrentStep((prev) => prev + 1)
      return true
    }
    return isValid
  }, [form, isLastStep])

  const back = useCallback(() => {
    if (!isFirstStep) {
      setCurrentStep((prev) => prev - 1)
    }
  }, [isFirstStep])

  const goToStep = useCallback((step: number) => {
    if (step >= 0 && step <= currentStep) {
      setCurrentStep(step)
    }
  }, [currentStep])

  const submit = useCallback(async () => {
    const isValid = await form.trigger()
    if (isValid) {
      setIsSubmitting(true)
      try {
        await onComplete(form.getValues())
      } finally {
        setIsSubmitting(false)
      }
    }
  }, [form, onComplete])

  return {
    currentStep,
    isFirstStep,
    isLastStep,
    isSubmitting,
    form,
    next,
    back,
    goToStep,
    submit,
  }
}
```

---

## 3. Form Validation

### Zod Schema Integration

```tsx
import { z } from 'zod'

// Common validation schemas
export const emailSchema = z
  .string()
  .min(1, 'Email is required')
  .email('Please enter a valid email address')

export const passwordSchema = z
  .string()
  .min(8, 'Password must be at least 8 characters')
  .regex(/[A-Z]/, 'Password must contain at least one uppercase letter')
  .regex(/[a-z]/, 'Password must contain at least one lowercase letter')
  .regex(/[0-9]/, 'Password must contain at least one number')

export const phoneSchema = z
  .string()
  .min(1, 'Phone number is required')
  .regex(/^\+?[0-9]{10,14}$/, 'Please enter a valid phone number')

export const urlSchema = z
  .string()
  .url('Please enter a valid URL')
  .optional()
  .or(z.literal(''))

export const dateSchema = z
  .date()
  .min(new Date(), 'Date must be in the future')

export const priceSchema = z
  .number()
  .min(0, 'Price cannot be negative')
  .max(100000, 'Price exceeds maximum allowed')

export const imageFileSchema = z
  .instanceof(File)
  .refine((file) => file.size <= 5 * 1024 * 1024, 'Image must be less than 5MB')
  .refine(
    (file) => ['image/jpeg', 'image/png', 'image/webp'].includes(file.type),
    'Image must be JPEG, PNG, or WebP'
  )

// Example form schema
export const contactFormSchema = z.object({
  name: z.string().min(1, 'Name is required').max(100, 'Name is too long'),
  email: emailSchema,
  phone: phoneSchema.optional(),
  subject: z.string().min(1, 'Subject is required'),
  message: z
    .string()
    .min(10, 'Message must be at least 10 characters')
    .max(1000, 'Message is too long'),
})

export type ContactFormData = z.infer<typeof contactFormSchema>
```

### React Hook Form Integration

```tsx
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'

function ContactForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
    reset,
  } = useForm<ContactFormData>({
    resolver: zodResolver(contactFormSchema),
    defaultValues: {
      name: '',
      email: '',
      phone: '',
      subject: '',
      message: '',
    },
  })

  const onSubmit = async (data: ContactFormData) => {
    try {
      await submitContactForm(data)
      reset()
      toast.success('Message sent successfully!')
    } catch (error) {
      toast.error('Failed to send message. Please try again.')
    }
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-6 max-w-lg">
      <Input
        label="Full Name"
        placeholder="Enter your name"
        error={errors.name?.message}
        {...register('name')}
      />

      <Input
        label="Email"
        type="email"
        placeholder="you@example.com"
        error={errors.email?.message}
        {...register('email')}
      />

      <Input
        label="Phone"
        type="tel"
        placeholder="+60 12 345 6789"
        error={errors.phone?.message}
        {...register('phone')}
      />

      <Input
        label="Subject"
        placeholder="What is this about?"
        error={errors.subject?.message}
        {...register('subject')}
      />

      <Textarea
        label="Message"
        placeholder="Tell us more..."
        rows={5}
        error={errors.message?.message}
        {...register('message')}
      />

      <Button
        type="submit"
        variant="solid"
        disabled={isSubmitting}
        className="w-full"
      >
        {isSubmitting ? (
          <>
            <LoaderIcon className="w-4 h-4 mr-2 animate-spin" />
            Sending...
          </>
        ) : (
          'Send Message'
        )}
      </Button>
    </form>
  )
}
```

### Inline Error Display

```tsx
interface FormFieldProps {
  label: string
  required?: boolean
  error?: string
  helperText?: string
  children: React.ReactNode
}

function FormField({
  label,
  required = false,
  error,
  helperText,
  children,
}: FormFieldProps) {
  const id = useId()

  return (
    <div className="space-y-1.5">
      <label
        htmlFor={id}
        className="block font-body text-body-sm font-medium text-gray-700"
      >
        {label}
        {required && (
          <span className="text-red-500 ml-1" aria-hidden="true">*</span>
        )}
        {!required && (
          <span className="text-gray-400 ml-1 font-normal">(optional)</span>
        )}
      </label>

      {React.cloneElement(children as React.ReactElement, {
        id,
        'aria-invalid': !!error,
        'aria-describedby': error ? `${id}-error` : helperText ? `${id}-helper` : undefined,
      })}

      {helperText && !error && (
        <p id={`${id}-helper`} className="text-caption text-gray-500">
          {helperText}
        </p>
      )}

      {error && (
        <p
          id={`${id}-error`}
          className="flex items-center gap-1 text-caption text-red-600"
          role="alert"
        >
          <AlertCircleIcon className="w-3.5 h-3.5 flex-shrink-0" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Error Summary Component

```tsx
interface FormErrorSummaryProps {
  errors: Record<string, { message?: string }>
  title?: string
}

function FormErrorSummary({
  errors,
  title = 'Please fix the following errors:',
}: FormErrorSummaryProps) {
  const errorList = Object.entries(errors)
    .filter(([_, error]) => error?.message)
    .map(([field, error]) => ({
      field,
      message: error.message!,
    }))

  if (errorList.length === 0) return null

  return (
    <div
      className="rounded-lg bg-red-50 border border-red-200 p-4"
      role="alert"
      aria-live="polite"
    >
      <div className="flex items-start gap-3">
        <AlertCircleIcon className="w-5 h-5 text-red-500 flex-shrink-0 mt-0.5" />
        <div>
          <h3 className="font-body text-body font-medium text-red-800">
            {title}
          </h3>
          <ul className="mt-2 space-y-1">
            {errorList.map(({ field, message }) => (
              <li key={field} className="text-body-sm text-red-700">
                <a
                  href={`#${field}`}
                  className="hover:underline"
                  onClick={(e) => {
                    e.preventDefault()
                    document.getElementById(field)?.focus()
                  }}
                >
                  {message}
                </a>
              </li>
            ))}
          </ul>
        </div>
      </div>
    </div>
  )
}
```

### Usage with Error Summary

```tsx
function RegistrationForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitted },
  } = useForm<RegistrationData>({
    resolver: zodResolver(registrationSchema),
  })

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-6">
      {isSubmitted && Object.keys(errors).length > 0 && (
        <FormErrorSummary errors={errors} />
      )}

      <Input
        id="email"
        label="Email"
        error={errors.email?.message}
        {...register('email')}
      />

      <Input
        id="password"
        label="Password"
        type="password"
        error={errors.password?.message}
        {...register('password')}
      />

      <Button type="submit" variant="solid">
        Create Account
      </Button>
    </form>
  )
}
```

---

## 4. Form Sections

### Grouping Related Fields

```tsx
interface FieldGroupProps {
  legend?: string
  description?: string
  children: React.ReactNode
  columns?: 1 | 2
}

function FieldGroup({
  legend,
  description,
  children,
  columns = 1,
}: FieldGroupProps) {
  return (
    <fieldset className="space-y-4">
      {(legend || description) && (
        <div className="mb-4">
          {legend && (
            <legend className="font-body text-body font-medium text-gray-900">
              {legend}
            </legend>
          )}
          {description && (
            <p className="text-body-sm text-gray-500 mt-1">{description}</p>
          )}
        </div>
      )}

      <div className={`
        ${columns === 2 ? 'grid grid-cols-1 md:grid-cols-2 gap-4' : 'space-y-4'}
      `}>
        {children}
      </div>
    </fieldset>
  )
}
```

### Collapsible Section

```tsx
interface CollapsibleSectionProps {
  title: string
  defaultOpen?: boolean
  children: React.ReactNode
}

function CollapsibleSection({
  title,
  defaultOpen = true,
  children,
}: CollapsibleSectionProps) {
  const [isOpen, setIsOpen] = useState(defaultOpen)

  return (
    <div className="border border-gray-200 rounded-xl overflow-hidden">
      <button
        type="button"
        onClick={() => setIsOpen(!isOpen)}
        className="w-full flex items-center justify-between p-4 bg-gray-50 hover:bg-gray-100 transition-colors"
        aria-expanded={isOpen}
      >
        <span className="font-heading text-h5 text-gray-900">{title}</span>
        <ChevronDownIcon
          className={`w-5 h-5 text-gray-500 transition-transform ${isOpen ? 'rotate-180' : ''}`}
        />
      </button>

      {isOpen && (
        <div className="p-4 border-t border-gray-200">
          {children}
        </div>
      )}
    </div>
  )
}
```

---

## 5. Required vs Optional Fields

### Field Marking Conventions

| Type | Label Treatment | Visual Indicator |
|------|-----------------|------------------|
| **Required** | Label with asterisk | Red `*` after label |
| **Optional** | Label with "(optional)" | Gray text after label |
| **Conditionally Required** | Dynamic indicator | Updates based on state |

### Implementation

```tsx
interface LabelProps {
  htmlFor: string
  children: React.ReactNode
  required?: boolean
  optional?: boolean
}

function Label({ htmlFor, children, required, optional }: LabelProps) {
  return (
    <label
      htmlFor={htmlFor}
      className="block font-body text-body-sm font-medium text-gray-700 mb-1.5"
    >
      {children}
      {required && (
        <>
          <span className="text-red-500 ml-1" aria-hidden="true">*</span>
          <span className="sr-only">(required)</span>
        </>
      )}
      {optional && (
        <span className="text-gray-400 font-normal ml-1">(optional)</span>
      )}
    </label>
  )
}
```

### Required Field Indicator Legend

```tsx
function RequiredFieldsLegend() {
  return (
    <p className="text-caption text-gray-500 mb-6">
      Fields marked with <span className="text-red-500">*</span> are required
    </p>
  )
}
```

---

## 6. Form Actions

### Action Button Patterns

```tsx
interface FormActionsProps {
  submitLabel?: string
  cancelLabel?: string
  onCancel?: () => void
  isSubmitting?: boolean
  showSaveDraft?: boolean
  onSaveDraft?: () => void
  alignment?: 'left' | 'right' | 'between' | 'center'
}

function FormActions({
  submitLabel = 'Submit',
  cancelLabel = 'Cancel',
  onCancel,
  isSubmitting = false,
  showSaveDraft = false,
  onSaveDraft,
  alignment = 'right',
}: FormActionsProps) {
  const alignmentClasses = {
    left: 'justify-start',
    right: 'justify-end',
    between: 'justify-between',
    center: 'justify-center',
  }

  return (
    <div className={`flex flex-wrap items-center gap-3 pt-6 border-t border-gray-200 ${alignmentClasses[alignment]}`}>
      {alignment === 'between' && showSaveDraft && (
        <Button
          type="button"
          variant="text"
          onClick={onSaveDraft}
          disabled={isSubmitting}
        >
          Save as Draft
        </Button>
      )}

      <div className="flex items-center gap-3">
        {onCancel && (
          <Button
            type="button"
            variant="outline"
            onClick={onCancel}
            disabled={isSubmitting}
          >
            {cancelLabel}
          </Button>
        )}

        <Button
          type="submit"
          variant="solid"
          disabled={isSubmitting}
        >
          {isSubmitting ? (
            <>
              <LoaderIcon className="w-4 h-4 mr-2 animate-spin" />
              Submitting...
            </>
          ) : (
            submitLabel
          )}
        </Button>
      </div>
    </div>
  )
}
```

### Sticky Form Actions (for long forms)

```tsx
function StickyFormActions({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <div className="sticky bottom-0 bg-white border-t border-gray-200 -mx-4 px-4 py-4 mt-8 shadow-[0_-4px_6px_-1px_rgba(0,0,0,0.1)]">
      <div className="max-w-2xl mx-auto">
        {children}
      </div>
    </div>
  )
}
```

---

## 7. Loading States

### Submit Button Loading

```tsx
interface SubmitButtonProps {
  isLoading?: boolean
  loadingText?: string
  children: React.ReactNode
}

function SubmitButton({
  isLoading = false,
  loadingText = 'Submitting...',
  children,
}: SubmitButtonProps) {
  return (
    <Button
      type="submit"
      variant="solid"
      disabled={isLoading}
      className="min-w-[120px]"
    >
      {isLoading ? (
        <>
          <LoaderIcon className="w-4 h-4 mr-2 animate-spin" />
          {loadingText}
        </>
      ) : (
        children
      )}
    </Button>
  )
}
```

### Form Overlay Loading

```tsx
interface FormLoadingOverlayProps {
  isLoading: boolean
  message?: string
}

function FormLoadingOverlay({
  isLoading,
  message = 'Processing...',
}: FormLoadingOverlayProps) {
  if (!isLoading) return null

  return (
    <div className="absolute inset-0 bg-white/80 backdrop-blur-sm flex items-center justify-center z-10 rounded-xl">
      <div className="text-center">
        <LoaderIcon className="w-8 h-8 text-mereka-teal animate-spin mx-auto" />
        <p className="text-body text-gray-600 mt-3">{message}</p>
      </div>
    </div>
  )
}

// Usage
function MyForm() {
  const [isSubmitting, setIsSubmitting] = useState(false)

  return (
    <div className="relative">
      <FormLoadingOverlay
        isLoading={isSubmitting}
        message="Creating your experience..."
      />

      <form className="space-y-6">
        {/* form fields */}
      </form>
    </div>
  )
}
```

### Field Loading State

```tsx
interface AsyncSelectProps {
  isLoading?: boolean
  options: SelectOption[]
  // ... other props
}

function AsyncSelect({ isLoading, options, ...props }: AsyncSelectProps) {
  return (
    <div className="relative">
      <Select options={options} {...props} />
      {isLoading && (
        <div className="absolute right-10 top-1/2 -translate-y-1/2">
          <LoaderIcon className="w-4 h-4 text-gray-400 animate-spin" />
        </div>
      )}
    </div>
  )
}
```

---

## 8. Success/Error Feedback

### Toast Notifications

```tsx
interface ToastProps {
  variant: 'success' | 'error' | 'warning' | 'info'
  title: string
  description?: string
  onClose?: () => void
}

function Toast({ variant, title, description, onClose }: ToastProps) {
  const styles = {
    success: {
      bg: 'bg-green-50',
      border: 'border-green-200',
      icon: CheckCircleIcon,
      iconColor: 'text-green-500',
      titleColor: 'text-green-800',
    },
    error: {
      bg: 'bg-red-50',
      border: 'border-red-200',
      icon: XCircleIcon,
      iconColor: 'text-red-500',
      titleColor: 'text-red-800',
    },
    warning: {
      bg: 'bg-yellow-50',
      border: 'border-yellow-200',
      icon: AlertTriangleIcon,
      iconColor: 'text-yellow-500',
      titleColor: 'text-yellow-800',
    },
    info: {
      bg: 'bg-blue-50',
      border: 'border-blue-200',
      icon: InfoIcon,
      iconColor: 'text-blue-500',
      titleColor: 'text-blue-800',
    },
  }

  const style = styles[variant]
  const Icon = style.icon

  return (
    <div className={`
      ${style.bg} ${style.border} border rounded-xl p-4
      flex items-start gap-3 shadow-lg max-w-sm
    `}>
      <Icon className={`w-5 h-5 ${style.iconColor} flex-shrink-0 mt-0.5`} />
      <div className="flex-1 min-w-0">
        <p className={`font-body text-body font-medium ${style.titleColor}`}>
          {title}
        </p>
        {description && (
          <p className="text-body-sm text-gray-600 mt-1">{description}</p>
        )}
      </div>
      {onClose && (
        <button
          onClick={onClose}
          className="text-gray-400 hover:text-gray-600 transition-colors"
        >
          <XIcon className="w-4 h-4" />
        </button>
      )}
    </div>
  )
}
```

### Inline Success Message

```tsx
interface SuccessMessageProps {
  title: string
  description?: string
  action?: {
    label: string
    onClick: () => void
  }
}

function SuccessMessage({ title, description, action }: SuccessMessageProps) {
  return (
    <div className="text-center py-12">
      <div className="w-16 h-16 rounded-full bg-green-100 flex items-center justify-center mx-auto">
        <CheckCircleIcon className="w-8 h-8 text-green-500" />
      </div>
      <h2 className="font-heading text-h3 text-gray-900 mt-6">{title}</h2>
      {description && (
        <p className="text-body text-gray-600 mt-2 max-w-md mx-auto">
          {description}
        </p>
      )}
      {action && (
        <Button
          variant="solid"
          className="mt-6"
          onClick={action.onClick}
        >
          {action.label}
        </Button>
      )}
    </div>
  )
}
```

### Form Error State

```tsx
interface FormErrorStateProps {
  title?: string
  description: string
  onRetry?: () => void
}

function FormErrorState({
  title = 'Something went wrong',
  description,
  onRetry,
}: FormErrorStateProps) {
  return (
    <div className="text-center py-12">
      <div className="w-16 h-16 rounded-full bg-red-100 flex items-center justify-center mx-auto">
        <XCircleIcon className="w-8 h-8 text-red-500" />
      </div>
      <h2 className="font-heading text-h3 text-gray-900 mt-6">{title}</h2>
      <p className="text-body text-gray-600 mt-2 max-w-md mx-auto">
        {description}
      </p>
      {onRetry && (
        <Button
          variant="outline"
          className="mt-6"
          onClick={onRetry}
        >
          <RefreshCwIcon className="w-4 h-4 mr-2" />
          Try Again
        </Button>
      )}
    </div>
  )
}
```

---

## Complete Form Examples

### Experience Creation Form (Multi-Step)

A comprehensive multi-step form for creating experiences on the platform.

```tsx
import { z } from 'zod'
import { useForm, FormProvider } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'

// Step 1: Basic Info Schema
const basicInfoSchema = z.object({
  title: z
    .string()
    .min(5, 'Title must be at least 5 characters')
    .max(100, 'Title is too long'),
  description: z
    .string()
    .min(50, 'Description must be at least 50 characters')
    .max(2000, 'Description is too long'),
  category: z.string().min(1, 'Please select a category'),
  themes: z
    .array(z.string())
    .min(1, 'Select at least one theme')
    .max(3, 'Maximum 3 themes allowed'),
  format: z.enum(['physical', 'virtual', 'hybrid']),
  location: z.string().optional(),
  virtualLink: z.string().url().optional(),
})

// Step 2: Schedule Schema
const scheduleSchema = z.object({
  scheduleType: z.enum(['single', 'recurring', 'on-demand']),
  startDate: z.date().min(new Date(), 'Start date must be in the future'),
  endDate: z.date().optional(),
  startTime: z.string().min(1, 'Start time is required'),
  duration: z.number().min(30, 'Minimum 30 minutes').max(480, 'Maximum 8 hours'),
  capacity: z.number().min(1, 'Minimum 1 participant').max(100, 'Maximum 100 participants'),
  recurringDays: z.array(z.string()).optional(),
})

// Step 3: Pricing Schema
const pricingSchema = z.object({
  pricingType: z.enum(['per-person', 'per-session', 'free']),
  price: z.number().min(0).optional(),
  currency: z.string().default('MYR'),
  earlyBirdDiscount: z.number().min(0).max(50).optional(),
  earlyBirdDeadline: z.date().optional(),
  cancellationPolicy: z.enum(['flexible', 'moderate', 'strict']),
})

// Step 4: Media Schema
const mediaSchema = z.object({
  coverImage: z.instanceof(File).optional(),
  galleryImages: z.array(z.instanceof(File)).max(8).optional(),
  videoUrl: z.string().url().optional().or(z.literal('')),
})

// Combined schema
const experienceSchema = basicInfoSchema
  .merge(scheduleSchema)
  .merge(pricingSchema)
  .merge(mediaSchema)

type ExperienceFormData = z.infer<typeof experienceSchema>

// Step definitions
const experienceSteps: Step[] = [
  {
    id: 'basic-info',
    title: 'Basic Information',
    description: 'Tell us about your experience',
  },
  {
    id: 'schedule',
    title: 'Schedule',
    description: 'Set your dates and availability',
  },
  {
    id: 'pricing',
    title: 'Pricing',
    description: 'Set your price and policies',
  },
  {
    id: 'media',
    title: 'Media',
    description: 'Add photos and videos',
  },
]

// Step 1: Basic Info Component
function BasicInfoStep() {
  const { register, formState: { errors }, watch, setValue } = useFormContext<ExperienceFormData>()
  const format = watch('format')

  return (
    <div className="space-y-6">
      <Input
        label="Experience Title"
        placeholder="e.g., Introduction to Pottery: Wheel Throwing"
        error={errors.title?.message}
        required
        {...register('title')}
      />

      <Textarea
        label="Description"
        placeholder="Describe what participants will learn and experience..."
        rows={5}
        error={errors.description?.message}
        helperText="Minimum 50 characters. Include what's included, what to bring, and who it's for."
        required
        {...register('description')}
      />

      <Select
        label="Category"
        placeholder="Select a category"
        options={[
          { value: 'arts-crafts', label: 'Arts & Crafts' },
          { value: 'cooking', label: 'Cooking & Culinary' },
          { value: 'wellness', label: 'Wellness & Mindfulness' },
          { value: 'music', label: 'Music & Performance' },
          { value: 'technology', label: 'Technology & Digital' },
          { value: 'outdoor', label: 'Outdoor & Adventure' },
        ]}
        error={errors.category?.message}
        required
        {...register('category')}
      />

      <CategorySelector
        label="Themes"
        options={[
          { value: 'beginner-friendly', label: 'Beginner Friendly' },
          { value: 'hands-on', label: 'Hands-on' },
          { value: 'creative', label: 'Creative' },
          { value: 'relaxing', label: 'Relaxing' },
          { value: 'skill-building', label: 'Skill Building' },
          { value: 'team-activity', label: 'Team Activity' },
        ]}
        value={watch('themes') || []}
        onChange={(value) => setValue('themes', value)}
        error={errors.themes?.message}
        maxSelections={3}
        required
      />

      <RadioGroup
        label="Format"
        name="format"
        options={[
          {
            value: 'physical',
            label: 'In-person',
            description: 'Participants meet at a physical location',
          },
          {
            value: 'virtual',
            label: 'Online',
            description: 'Conducted via video call',
          },
          {
            value: 'hybrid',
            label: 'Hybrid',
            description: 'Both in-person and online options',
          },
        ]}
        value={watch('format')}
        onChange={(value) => setValue('format', value as 'physical' | 'virtual' | 'hybrid')}
        error={errors.format?.message}
      />

      {(format === 'physical' || format === 'hybrid') && (
        <Input
          label="Location"
          placeholder="Enter the venue address"
          leadingIcon={<MapPinIcon className="w-5 h-5" />}
          error={errors.location?.message}
          {...register('location')}
        />
      )}

      {(format === 'virtual' || format === 'hybrid') && (
        <Input
          label="Virtual Meeting Link"
          placeholder="https://zoom.us/j/..."
          helperText="You can add this later before the session"
          error={errors.virtualLink?.message}
          {...register('virtualLink')}
        />
      )}
    </div>
  )
}

// Step 2: Schedule Component
function ScheduleStep() {
  const { register, formState: { errors }, watch, setValue } = useFormContext<ExperienceFormData>()
  const scheduleType = watch('scheduleType')

  return (
    <div className="space-y-6">
      <RadioGroup
        label="Schedule Type"
        name="scheduleType"
        options={[
          {
            value: 'single',
            label: 'Single Session',
            description: 'One-time event on a specific date',
          },
          {
            value: 'recurring',
            label: 'Recurring',
            description: 'Repeats on selected days',
          },
          {
            value: 'on-demand',
            label: 'On-demand',
            description: 'Participants request preferred times',
          },
        ]}
        value={scheduleType}
        onChange={(value) => setValue('scheduleType', value as 'single' | 'recurring' | 'on-demand')}
      />

      {scheduleType !== 'on-demand' && (
        <>
          <FormRow>
            <DatePicker
              label="Start Date"
              value={watch('startDate')}
              onChange={(date) => setValue('startDate', date)}
              minDate={new Date()}
              error={errors.startDate?.message}
              required
            />

            <TimePicker
              label="Start Time"
              value={watch('startTime')}
              onChange={(time) => setValue('startTime', time)}
              error={errors.startTime?.message}
              required
            />
          </FormRow>

          <TimePicker
            label="Duration"
            type="duration"
            value={watch('duration')?.toString()}
            onChange={(duration) => setValue('duration', parseInt(duration))}
            error={errors.duration?.message}
            required
          />
        </>
      )}

      {scheduleType === 'recurring' && (
        <CheckboxGroup
          label="Repeat on"
          options={[
            { value: 'monday', label: 'Mon' },
            { value: 'tuesday', label: 'Tue' },
            { value: 'wednesday', label: 'Wed' },
            { value: 'thursday', label: 'Thu' },
            { value: 'friday', label: 'Fri' },
            { value: 'saturday', label: 'Sat' },
            { value: 'sunday', label: 'Sun' },
          ]}
          value={watch('recurringDays') || []}
          onChange={(value) => setValue('recurringDays', value)}
        />
      )}

      <Input
        label="Maximum Participants"
        type="number"
        min={1}
        max={100}
        error={errors.capacity?.message}
        helperText="How many people can join each session?"
        required
        {...register('capacity', { valueAsNumber: true })}
      />
    </div>
  )
}

// Step 3: Pricing Component
function PricingStep() {
  const { register, formState: { errors }, watch, setValue } = useFormContext<ExperienceFormData>()
  const pricingType = watch('pricingType')

  return (
    <div className="space-y-6">
      <RadioGroup
        label="Pricing Model"
        name="pricingType"
        options={[
          {
            value: 'per-person',
            label: 'Per person',
            description: 'Each participant pays individually',
          },
          {
            value: 'per-session',
            label: 'Per session',
            description: 'Fixed price for the entire session',
          },
          {
            value: 'free',
            label: 'Free',
            description: 'No charge for participants',
          },
        ]}
        value={pricingType}
        onChange={(value) => setValue('pricingType', value as 'per-person' | 'per-session' | 'free')}
      />

      {pricingType !== 'free' && (
        <>
          <FormRow>
            <Select
              label="Currency"
              options={[
                { value: 'MYR', label: 'MYR (RM)' },
                { value: 'SGD', label: 'SGD (S$)' },
                { value: 'USD', label: 'USD ($)' },
              ]}
              {...register('currency')}
            />

            <Input
              label={pricingType === 'per-person' ? 'Price per person' : 'Session price'}
              type="number"
              min={0}
              leadingIcon={<span className="text-gray-400">RM</span>}
              error={errors.price?.message}
              required
              {...register('price', { valueAsNumber: true })}
            />
          </FormRow>

          <CollapsibleSection title="Early Bird Discount" defaultOpen={false}>
            <div className="space-y-4">
              <Input
                label="Discount Percentage"
                type="number"
                min={0}
                max={50}
                trailingIcon={<span className="text-gray-400">%</span>}
                helperText="Maximum 50% discount"
                {...register('earlyBirdDiscount', { valueAsNumber: true })}
              />

              <DatePicker
                label="Early Bird Deadline"
                value={watch('earlyBirdDeadline')}
                onChange={(date) => setValue('earlyBirdDeadline', date)}
                maxDate={watch('startDate')}
              />
            </div>
          </CollapsibleSection>
        </>
      )}

      <Select
        label="Cancellation Policy"
        options={[
          {
            value: 'flexible',
            label: 'Flexible - Full refund up to 24 hours before',
          },
          {
            value: 'moderate',
            label: 'Moderate - Full refund up to 5 days before',
          },
          {
            value: 'strict',
            label: 'Strict - 50% refund up to 7 days before',
          },
        ]}
        error={errors.cancellationPolicy?.message}
        required
        {...register('cancellationPolicy')}
      />
    </div>
  )
}

// Step 4: Media Component
function MediaStep() {
  const { formState: { errors }, watch, setValue } = useFormContext<ExperienceFormData>()

  return (
    <div className="space-y-6">
      <FileUpload
        type="cover"
        label="Cover Image"
        value={watch('coverImage')}
        onChange={(file) => setValue('coverImage', file as File)}
        error={errors.coverImage?.message}
        helperText="Recommended size: 1200x800px. Max 5MB."
      />

      <FileUpload
        type="gallery"
        label="Gallery Images"
        value={watch('galleryImages')}
        onChange={(files) => setValue('galleryImages', files as File[])}
        error={errors.galleryImages?.message}
        maxFiles={8}
        helperText="Add up to 8 photos showing different aspects of your experience."
      />

      <Input
        label="Video URL"
        placeholder="https://youtube.com/watch?v=..."
        helperText="Link to a YouTube or Vimeo video showcasing your experience"
        leadingIcon={<VideoIcon className="w-5 h-5" />}
        {...register('videoUrl')}
      />
    </div>
  )
}

// Main Experience Creation Form
export function ExperienceCreationForm() {
  const [currentStep, setCurrentStep] = useState(0)
  const [isSubmitting, setIsSubmitting] = useState(false)
  const [submitError, setSubmitError] = useState<string | null>(null)
  const [isSuccess, setIsSuccess] = useState(false)

  const methods = useForm<ExperienceFormData>({
    resolver: zodResolver(experienceSchema),
    defaultValues: {
      title: '',
      description: '',
      category: '',
      themes: [],
      format: 'physical',
      scheduleType: 'single',
      capacity: 10,
      currency: 'MYR',
      pricingType: 'per-person',
      cancellationPolicy: 'moderate',
    },
    mode: 'onBlur',
  })

  const stepSchemas = [basicInfoSchema, scheduleSchema, pricingSchema, mediaSchema]

  const handleNext = async () => {
    // Validate current step
    const currentSchema = stepSchemas[currentStep]
    const currentFields = Object.keys(currentSchema.shape)
    const isValid = await methods.trigger(currentFields as any)

    if (isValid && currentStep < experienceSteps.length - 1) {
      setCurrentStep((prev) => prev + 1)
      window.scrollTo({ top: 0, behavior: 'smooth' })
    }
  }

  const handleBack = () => {
    if (currentStep > 0) {
      setCurrentStep((prev) => prev - 1)
      window.scrollTo({ top: 0, behavior: 'smooth' })
    }
  }

  const handleSubmit = async (data: ExperienceFormData) => {
    setIsSubmitting(true)
    setSubmitError(null)

    try {
      // Upload images and create experience
      await createExperience(data)
      setIsSuccess(true)
    } catch (error) {
      setSubmitError(
        error instanceof Error
          ? error.message
          : 'Failed to create experience. Please try again.'
      )
    } finally {
      setIsSubmitting(false)
    }
  }

  if (isSuccess) {
    return (
      <SuccessMessage
        title="Experience Created!"
        description="Your experience has been submitted for review. We'll notify you once it's approved."
        action={{
          label: 'View My Experiences',
          onClick: () => router.push('/dashboard/experiences'),
        }}
      />
    )
  }

  const stepComponents = [
    <BasicInfoStep key="basic" />,
    <ScheduleStep key="schedule" />,
    <PricingStep key="pricing" />,
    <MediaStep key="media" />,
  ]

  return (
    <FormProvider {...methods}>
      <form onSubmit={methods.handleSubmit(handleSubmit)}>
        <MultiStepForm
          steps={experienceSteps}
          currentStep={currentStep}
          onNext={handleNext}
          onBack={handleBack}
          onSubmit={handleSubmit}
          isSubmitting={isSubmitting}
        >
          {submitError && (
            <div className="mb-6">
              <Toast
                variant="error"
                title="Submission Failed"
                description={submitError}
                onClose={() => setSubmitError(null)}
              />
            </div>
          )}

          {stepComponents[currentStep]}
        </MultiStepForm>
      </form>
    </FormProvider>
  )
}
```

---

### Booking Form

Complete booking form with date selection, attendee info, and payment.

```tsx
import { z } from 'zod'
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'

const bookingSchema = z.object({
  // Session selection
  sessionId: z.string().min(1, 'Please select a session'),
  quantity: z.number().min(1).max(10),

  // Attendee info
  attendees: z.array(z.object({
    name: z.string().min(1, 'Name is required'),
    email: z.string().email('Invalid email'),
    phone: z.string().optional(),
    dietaryRequirements: z.string().optional(),
  })).min(1),

  // Contact info
  contactName: z.string().min(1, 'Name is required'),
  contactEmail: z.string().email('Invalid email'),
  contactPhone: z.string().min(1, 'Phone is required'),

  // Additional
  specialRequests: z.string().optional(),
  agreeToTerms: z.boolean().refine((val) => val === true, {
    message: 'You must agree to the terms and conditions',
  }),
})

type BookingFormData = z.infer<typeof bookingSchema>

interface BookingFormProps {
  experience: Experience
  sessions: Session[]
  onSuccess: (booking: Booking) => void
}

export function BookingForm({
  experience,
  sessions,
  onSuccess,
}: BookingFormProps) {
  const [isSubmitting, setIsSubmitting] = useState(false)

  const {
    register,
    handleSubmit,
    watch,
    setValue,
    formState: { errors },
  } = useForm<BookingFormData>({
    resolver: zodResolver(bookingSchema),
    defaultValues: {
      quantity: 1,
      attendees: [{ name: '', email: '', phone: '', dietaryRequirements: '' }],
      agreeToTerms: false,
    },
  })

  const selectedSessionId = watch('sessionId')
  const quantity = watch('quantity')
  const selectedSession = sessions.find((s) => s.id === selectedSessionId)

  const totalPrice = selectedSession
    ? selectedSession.pricePerPerson * quantity
    : 0

  // Update attendees array when quantity changes
  useEffect(() => {
    const currentAttendees = watch('attendees')
    if (quantity > currentAttendees.length) {
      setValue('attendees', [
        ...currentAttendees,
        ...Array(quantity - currentAttendees.length).fill({
          name: '',
          email: '',
          phone: '',
          dietaryRequirements: '',
        }),
      ])
    } else if (quantity < currentAttendees.length) {
      setValue('attendees', currentAttendees.slice(0, quantity))
    }
  }, [quantity, setValue, watch])

  const onSubmit = async (data: BookingFormData) => {
    setIsSubmitting(true)
    try {
      const booking = await createBooking({
        experienceId: experience.id,
        ...data,
      })
      onSuccess(booking)
    } catch (error) {
      toast.error('Failed to create booking. Please try again.')
    } finally {
      setIsSubmitting(false)
    }
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)} className="space-y-8">
      {/* Session Selection */}
      <FormSection title="Select a Session">
        <div className="space-y-3">
          {sessions.map((session) => {
            const isSelected = selectedSessionId === session.id
            const isSoldOut = session.spotsLeft === 0

            return (
              <label
                key={session.id}
                className={`
                  block p-4 rounded-xl border-2 cursor-pointer transition-all
                  ${isSelected
                    ? 'border-mereka-teal bg-mereka-teal/5'
                    : 'border-gray-200 hover:border-gray-300'
                  }
                  ${isSoldOut ? 'opacity-50 cursor-not-allowed' : ''}
                `}
              >
                <input
                  type="radio"
                  value={session.id}
                  disabled={isSoldOut}
                  className="sr-only"
                  {...register('sessionId')}
                />

                <div className="flex items-center justify-between">
                  <div>
                    <p className="font-heading text-h5 text-gray-900">
                      {formatDate(session.date)}
                    </p>
                    <p className="text-body-sm text-gray-600 mt-1">
                      {session.startTime} - {session.endTime}
                    </p>
                  </div>

                  <div className="text-right">
                    <p className="font-heading text-h5 text-gray-900">
                      RM {session.pricePerPerson}
                    </p>
                    {isSoldOut ? (
                      <span className="text-caption text-red-500">Sold out</span>
                    ) : (
                      <span className="text-caption text-gray-500">
                        {session.spotsLeft} spots left
                      </span>
                    )}
                  </div>
                </div>
              </label>
            )
          })}
        </div>
        {errors.sessionId && (
          <p className="text-caption text-red-600 mt-2 flex items-center gap-1">
            <AlertCircleIcon className="w-3.5 h-3.5" />
            {errors.sessionId.message}
          </p>
        )}
      </FormSection>

      {/* Quantity */}
      {selectedSession && (
        <FormSection title="Number of Participants">
          <div className="flex items-center gap-4">
            <div className="flex items-center border border-gray-300 rounded-lg">
              <button
                type="button"
                onClick={() => quantity > 1 && setValue('quantity', quantity - 1)}
                disabled={quantity <= 1}
                className="w-10 h-10 flex items-center justify-center text-gray-600 hover:bg-gray-100 disabled:opacity-50"
              >
                <MinusIcon className="w-4 h-4" />
              </button>
              <span className="w-12 text-center font-medium">{quantity}</span>
              <button
                type="button"
                onClick={() =>
                  quantity < Math.min(10, selectedSession.spotsLeft) &&
                  setValue('quantity', quantity + 1)
                }
                disabled={quantity >= Math.min(10, selectedSession.spotsLeft)}
                className="w-10 h-10 flex items-center justify-center text-gray-600 hover:bg-gray-100 disabled:opacity-50"
              >
                <PlusIcon className="w-4 h-4" />
              </button>
            </div>
            <span className="text-body-sm text-gray-500">
              Max {Math.min(10, selectedSession.spotsLeft)} per booking
            </span>
          </div>
        </FormSection>
      )}

      {/* Attendee Information */}
      {selectedSession && quantity > 0 && (
        <FormSection
          title="Attendee Information"
          description="Please provide details for each participant"
        >
          <div className="space-y-6">
            {Array.from({ length: quantity }).map((_, index) => (
              <div
                key={index}
                className="p-4 bg-gray-50 rounded-xl space-y-4"
              >
                <p className="font-medium text-gray-900">
                  Attendee {index + 1}
                  {index === 0 && (
                    <span className="text-gray-400 font-normal ml-2">
                      (Primary contact)
                    </span>
                  )}
                </p>

                <FormRow>
                  <Input
                    label="Full Name"
                    error={errors.attendees?.[index]?.name?.message}
                    required
                    {...register(`attendees.${index}.name`)}
                  />
                  <Input
                    label="Email"
                    type="email"
                    error={errors.attendees?.[index]?.email?.message}
                    required
                    {...register(`attendees.${index}.email`)}
                  />
                </FormRow>

                <FormRow>
                  <Input
                    label="Phone"
                    type="tel"
                    error={errors.attendees?.[index]?.phone?.message}
                    {...register(`attendees.${index}.phone`)}
                  />
                  <Input
                    label="Dietary Requirements"
                    placeholder="e.g., Vegetarian, allergies"
                    {...register(`attendees.${index}.dietaryRequirements`)}
                  />
                </FormRow>
              </div>
            ))}
          </div>
        </FormSection>
      )}

      {/* Contact Information */}
      <FormSection
        title="Contact Information"
        description="We'll send booking confirmation to this contact"
      >
        <FormRow>
          <Input
            label="Full Name"
            error={errors.contactName?.message}
            required
            {...register('contactName')}
          />
          <Input
            label="Email"
            type="email"
            error={errors.contactEmail?.message}
            required
            {...register('contactEmail')}
          />
        </FormRow>
        <Input
          label="Phone Number"
          type="tel"
          error={errors.contactPhone?.message}
          required
          {...register('contactPhone')}
        />
      </FormSection>

      {/* Special Requests */}
      <FormSection title="Special Requests" description="Optional">
        <Textarea
          placeholder="Any special requirements or questions for the host..."
          rows={3}
          {...register('specialRequests')}
        />
      </FormSection>

      {/* Terms and Conditions */}
      <div className="pt-6 border-t border-gray-200">
        <Checkbox
          label="I agree to the Terms of Service and Cancellation Policy"
          error={errors.agreeToTerms?.message}
          {...register('agreeToTerms')}
        />
      </div>

      {/* Order Summary & Submit */}
      <div className="bg-gray-50 rounded-xl p-6">
        <h3 className="font-heading text-h5 text-gray-900 mb-4">
          Order Summary
        </h3>

        <div className="space-y-2 text-body-sm">
          <div className="flex justify-between">
            <span className="text-gray-600">{experience.title}</span>
          </div>
          {selectedSession && (
            <>
              <div className="flex justify-between">
                <span className="text-gray-600">
                  {formatDate(selectedSession.date)} at {selectedSession.startTime}
                </span>
              </div>
              <div className="flex justify-between">
                <span className="text-gray-600">
                  RM {selectedSession.pricePerPerson} x {quantity} {quantity === 1 ? 'person' : 'people'}
                </span>
                <span className="text-gray-900">RM {totalPrice}</span>
              </div>
            </>
          )}
        </div>

        <div className="flex justify-between items-center mt-4 pt-4 border-t border-gray-200">
          <span className="font-heading text-h4 text-gray-900">Total</span>
          <span className="font-heading text-h3 text-gray-900">
            RM {totalPrice}
          </span>
        </div>

        <Button
          type="submit"
          variant="solid"
          size="large"
          disabled={isSubmitting || !selectedSession}
          className="w-full mt-6"
        >
          {isSubmitting ? (
            <>
              <LoaderIcon className="w-5 h-5 mr-2 animate-spin" />
              Processing...
            </>
          ) : (
            `Pay RM ${totalPrice}`
          )}
        </Button>

        <p className="text-caption text-gray-500 text-center mt-3">
          You won't be charged yet. Payment will be processed securely.
        </p>
      </div>
    </form>
  )
}
```

---

### Profile Settings Form (Single Page)

Complete profile settings form with sections.

```tsx
import { z } from 'zod'
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'

const profileSchema = z.object({
  // Personal info
  firstName: z.string().min(1, 'First name is required'),
  lastName: z.string().min(1, 'Last name is required'),
  email: z.string().email('Invalid email'),
  phone: z.string().optional(),
  bio: z.string().max(500, 'Bio must be less than 500 characters').optional(),
  avatar: z.instanceof(File).optional(),

  // Preferences
  language: z.string(),
  timezone: z.string(),
  currency: z.string(),

  // Notifications
  emailNotifications: z.boolean(),
  pushNotifications: z.boolean(),
  smsNotifications: z.boolean(),
  marketingEmails: z.boolean(),

  // Privacy
  publicProfile: z.boolean(),
  showBookingHistory: z.boolean(),
})

type ProfileFormData = z.infer<typeof profileSchema>

interface ProfileSettingsFormProps {
  initialData: ProfileFormData
  onSave: (data: ProfileFormData) => Promise<void>
}

export function ProfileSettingsForm({
  initialData,
  onSave,
}: ProfileSettingsFormProps) {
  const [isSubmitting, setIsSubmitting] = useState(false)
  const [isSaved, setIsSaved] = useState(false)

  const {
    register,
    handleSubmit,
    watch,
    setValue,
    formState: { errors, isDirty },
  } = useForm<ProfileFormData>({
    resolver: zodResolver(profileSchema),
    defaultValues: initialData,
  })

  const onSubmit = async (data: ProfileFormData) => {
    setIsSubmitting(true)
    try {
      await onSave(data)
      setIsSaved(true)
      setTimeout(() => setIsSaved(false), 3000)
    } catch (error) {
      toast.error('Failed to save changes. Please try again.')
    } finally {
      setIsSubmitting(false)
    }
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <SectionedForm
        title="Profile Settings"
        description="Manage your personal information and preferences"
      >
        {/* Avatar Section */}
        <FormSection title="Profile Photo">
          <div className="flex items-center gap-6">
            <div className="relative">
              <Avatar
                src={watch('avatar') ? URL.createObjectURL(watch('avatar')!) : initialData.avatar}
                alt={`${watch('firstName')} ${watch('lastName')}`}
                size="2xl"
              />
              <label className="absolute bottom-0 right-0 w-8 h-8 bg-gray-900 rounded-full flex items-center justify-center cursor-pointer hover:bg-gray-800 transition-colors">
                <CameraIcon className="w-4 h-4 text-white" />
                <input
                  type="file"
                  accept="image/*"
                  className="sr-only"
                  onChange={(e) => {
                    const file = e.target.files?.[0]
                    if (file) setValue('avatar', file, { shouldDirty: true })
                  }}
                />
              </label>
            </div>
            <div>
              <p className="text-body font-medium text-gray-900">
                Upload a new photo
              </p>
              <p className="text-body-sm text-gray-500 mt-1">
                JPG or PNG. Max 5MB.
              </p>
            </div>
          </div>
        </FormSection>

        {/* Personal Information */}
        <FormSection
          title="Personal Information"
          description="Your public profile information"
        >
          <FormRow>
            <Input
              label="First Name"
              error={errors.firstName?.message}
              required
              {...register('firstName')}
            />
            <Input
              label="Last Name"
              error={errors.lastName?.message}
              required
              {...register('lastName')}
            />
          </FormRow>

          <Input
            label="Email"
            type="email"
            error={errors.email?.message}
            required
            helperText="Your email is used for login and notifications"
            {...register('email')}
          />

          <Input
            label="Phone Number"
            type="tel"
            error={errors.phone?.message}
            {...register('phone')}
          />

          <Textarea
            label="Bio"
            placeholder="Tell us a bit about yourself..."
            rows={4}
            error={errors.bio?.message}
            helperText={`${watch('bio')?.length || 0}/500 characters`}
            {...register('bio')}
          />
        </FormSection>

        {/* Preferences */}
        <FormSection
          title="Preferences"
          description="Customize your experience"
        >
          <FormRow columns={3}>
            <Select
              label="Language"
              options={[
                { value: 'en', label: 'English' },
                { value: 'ms', label: 'Bahasa Malaysia' },
                { value: 'zh', label: 'Chinese (Simplified)' },
              ]}
              {...register('language')}
            />

            <Select
              label="Timezone"
              options={[
                { value: 'Asia/Kuala_Lumpur', label: 'Kuala Lumpur (GMT+8)' },
                { value: 'Asia/Singapore', label: 'Singapore (GMT+8)' },
                { value: 'Asia/Jakarta', label: 'Jakarta (GMT+7)' },
              ]}
              {...register('timezone')}
            />

            <Select
              label="Currency"
              options={[
                { value: 'MYR', label: 'MYR (RM)' },
                { value: 'SGD', label: 'SGD (S$)' },
                { value: 'USD', label: 'USD ($)' },
              ]}
              {...register('currency')}
            />
          </FormRow>
        </FormSection>

        {/* Notifications */}
        <FormSection
          title="Notifications"
          description="Choose how you want to be notified"
        >
          <div className="space-y-4">
            <Toggle
              label="Email notifications"
              description="Receive booking confirmations and updates via email"
              checked={watch('emailNotifications')}
              onChange={(checked) => setValue('emailNotifications', checked, { shouldDirty: true })}
            />

            <Toggle
              label="Push notifications"
              description="Get instant alerts on your device"
              checked={watch('pushNotifications')}
              onChange={(checked) => setValue('pushNotifications', checked, { shouldDirty: true })}
            />

            <Toggle
              label="SMS notifications"
              description="Receive text messages for important updates"
              checked={watch('smsNotifications')}
              onChange={(checked) => setValue('smsNotifications', checked, { shouldDirty: true })}
            />

            <Toggle
              label="Marketing emails"
              description="Receive news, tips, and special offers"
              checked={watch('marketingEmails')}
              onChange={(checked) => setValue('marketingEmails', checked, { shouldDirty: true })}
            />
          </div>
        </FormSection>

        {/* Privacy */}
        <FormSection
          title="Privacy"
          description="Control who can see your information"
        >
          <div className="space-y-4">
            <Toggle
              label="Public profile"
              description="Allow others to view your profile and reviews"
              checked={watch('publicProfile')}
              onChange={(checked) => setValue('publicProfile', checked, { shouldDirty: true })}
            />

            <Toggle
              label="Show booking history"
              description="Display your past experiences on your public profile"
              checked={watch('showBookingHistory')}
              onChange={(checked) => setValue('showBookingHistory', checked, { shouldDirty: true })}
            />
          </div>
        </FormSection>

        {/* Actions */}
        <StickyFormActions>
          <div className="flex items-center justify-between">
            <div>
              {isSaved && (
                <span className="text-body-sm text-green-600 flex items-center gap-1">
                  <CheckCircleIcon className="w-4 h-4" />
                  Changes saved successfully
                </span>
              )}
            </div>

            <div className="flex items-center gap-3">
              <Button
                type="button"
                variant="outline"
                disabled={!isDirty || isSubmitting}
                onClick={() => reset(initialData)}
              >
                Discard Changes
              </Button>

              <Button
                type="submit"
                variant="solid"
                disabled={!isDirty || isSubmitting}
              >
                {isSubmitting ? (
                  <>
                    <LoaderIcon className="w-4 h-4 mr-2 animate-spin" />
                    Saving...
                  </>
                ) : (
                  'Save Changes'
                )}
              </Button>
            </div>
          </div>
        </StickyFormActions>
      </SectionedForm>
    </form>
  )
}
```

---

### Hub Onboarding Form (Multi-Step)

Multi-step onboarding form for new hub registration.

```tsx
import { z } from 'zod'
import { useForm, FormProvider } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'

// Step schemas
const hubBasicsSchema = z.object({
  hubName: z.string().min(2, 'Hub name is required').max(100),
  hubType: z.enum(['individual', 'organization', 'venue']),
  description: z.string().min(50, 'Description must be at least 50 characters'),
  logo: z.instanceof(File).optional(),
})

const hubLocationSchema = z.object({
  address: z.string().min(1, 'Address is required'),
  city: z.string().min(1, 'City is required'),
  state: z.string().min(1, 'State is required'),
  postalCode: z.string().min(1, 'Postal code is required'),
  country: z.string().min(1, 'Country is required'),
  operatingHours: z.array(z.object({
    day: z.string(),
    isOpen: z.boolean(),
    openTime: z.string().optional(),
    closeTime: z.string().optional(),
  })),
})

const hubCategoriesSchema = z.object({
  categories: z.array(z.string()).min(1, 'Select at least one category'),
  targetAudience: z.array(z.string()).min(1, 'Select at least one audience'),
  experienceTypes: z.array(z.string()).min(1, 'Select at least one type'),
})

const hubVerificationSchema = z.object({
  businessRegistration: z.instanceof(File).optional(),
  ownerIdDocument: z.instanceof(File),
  agreeToTerms: z.boolean().refine((val) => val, 'You must agree to the terms'),
  agreeToHostGuidelines: z.boolean().refine((val) => val, 'You must agree to the guidelines'),
})

const hubOnboardingSchema = hubBasicsSchema
  .merge(hubLocationSchema)
  .merge(hubCategoriesSchema)
  .merge(hubVerificationSchema)

type HubOnboardingData = z.infer<typeof hubOnboardingSchema>

const onboardingSteps: Step[] = [
  {
    id: 'basics',
    title: 'Hub Basics',
    description: 'Tell us about your hub',
  },
  {
    id: 'location',
    title: 'Location & Hours',
    description: 'Where are you located?',
  },
  {
    id: 'categories',
    title: 'Categories',
    description: 'What do you offer?',
  },
  {
    id: 'verification',
    title: 'Verification',
    description: 'Verify your identity',
  },
]

// Step Components
function HubBasicsStep() {
  const { register, formState: { errors }, watch, setValue } = useFormContext<HubOnboardingData>()

  return (
    <div className="space-y-6">
      <div className="flex flex-col items-center mb-8">
        <div className="relative">
          {watch('logo') ? (
            <img
              src={URL.createObjectURL(watch('logo')!)}
              alt="Hub logo"
              className="w-24 h-24 rounded-2xl object-cover"
            />
          ) : (
            <div className="w-24 h-24 rounded-2xl bg-gray-100 flex items-center justify-center">
              <BuildingIcon className="w-10 h-10 text-gray-400" />
            </div>
          )}
          <label className="absolute -bottom-2 -right-2 w-8 h-8 bg-mereka-teal rounded-full flex items-center justify-center cursor-pointer hover:bg-mereka-teal/90 transition-colors">
            <PlusIcon className="w-4 h-4 text-white" />
            <input
              type="file"
              accept="image/*"
              className="sr-only"
              onChange={(e) => {
                const file = e.target.files?.[0]
                if (file) setValue('logo', file)
              }}
            />
          </label>
        </div>
        <p className="text-body-sm text-gray-500 mt-3">
          Upload your hub logo
        </p>
      </div>

      <Input
        label="Hub Name"
        placeholder="e.g., Creative Arts Studio"
        error={errors.hubName?.message}
        required
        {...register('hubName')}
      />

      <RadioGroup
        label="Hub Type"
        name="hubType"
        options={[
          {
            value: 'individual',
            label: 'Individual',
            description: 'You operate as a solo creator or instructor',
          },
          {
            value: 'organization',
            label: 'Organization',
            description: 'You represent a company or group',
          },
          {
            value: 'venue',
            label: 'Venue',
            description: 'You manage a physical space for events',
          },
        ]}
        value={watch('hubType')}
        onChange={(value) => setValue('hubType', value as any)}
        error={errors.hubType?.message}
      />

      <Textarea
        label="Description"
        placeholder="Tell potential learners about your hub, your expertise, and what makes you unique..."
        rows={5}
        error={errors.description?.message}
        helperText="Minimum 50 characters. This will appear on your public profile."
        required
        {...register('description')}
      />
    </div>
  )
}

function HubLocationStep() {
  const { register, formState: { errors }, watch, setValue } = useFormContext<HubOnboardingData>()

  const defaultHours = [
    { day: 'Monday', isOpen: true, openTime: '09:00', closeTime: '18:00' },
    { day: 'Tuesday', isOpen: true, openTime: '09:00', closeTime: '18:00' },
    { day: 'Wednesday', isOpen: true, openTime: '09:00', closeTime: '18:00' },
    { day: 'Thursday', isOpen: true, openTime: '09:00', closeTime: '18:00' },
    { day: 'Friday', isOpen: true, openTime: '09:00', closeTime: '18:00' },
    { day: 'Saturday', isOpen: true, openTime: '10:00', closeTime: '16:00' },
    { day: 'Sunday', isOpen: false, openTime: '', closeTime: '' },
  ]

  useEffect(() => {
    if (!watch('operatingHours')?.length) {
      setValue('operatingHours', defaultHours)
    }
  }, [])

  return (
    <div className="space-y-6">
      <Input
        label="Street Address"
        placeholder="123 Creative Lane"
        error={errors.address?.message}
        required
        {...register('address')}
      />

      <FormRow>
        <Input
          label="City"
          error={errors.city?.message}
          required
          {...register('city')}
        />
        <Input
          label="State/Province"
          error={errors.state?.message}
          required
          {...register('state')}
        />
      </FormRow>

      <FormRow>
        <Input
          label="Postal Code"
          error={errors.postalCode?.message}
          required
          {...register('postalCode')}
        />
        <Select
          label="Country"
          options={[
            { value: 'MY', label: 'Malaysia' },
            { value: 'SG', label: 'Singapore' },
            { value: 'ID', label: 'Indonesia' },
          ]}
          error={errors.country?.message}
          required
          {...register('country')}
        />
      </FormRow>

      <FieldGroup legend="Operating Hours" description="When is your hub open?">
        <div className="space-y-3">
          {(watch('operatingHours') || defaultHours).map((hours, index) => (
            <div
              key={hours.day}
              className="flex items-center gap-4 py-2"
            >
              <div className="w-24">
                <Toggle
                  label={hours.day.slice(0, 3)}
                  checked={hours.isOpen}
                  onChange={(checked) => {
                    const updated = [...watch('operatingHours')]
                    updated[index] = { ...updated[index], isOpen: checked }
                    setValue('operatingHours', updated)
                  }}
                />
              </div>

              {hours.isOpen && (
                <div className="flex items-center gap-2">
                  <Input
                    type="time"
                    value={hours.openTime}
                    onChange={(e) => {
                      const updated = [...watch('operatingHours')]
                      updated[index] = { ...updated[index], openTime: e.target.value }
                      setValue('operatingHours', updated)
                    }}
                    className="w-28"
                  />
                  <span className="text-gray-400">to</span>
                  <Input
                    type="time"
                    value={hours.closeTime}
                    onChange={(e) => {
                      const updated = [...watch('operatingHours')]
                      updated[index] = { ...updated[index], closeTime: e.target.value }
                      setValue('operatingHours', updated)
                    }}
                    className="w-28"
                  />
                </div>
              )}

              {!hours.isOpen && (
                <span className="text-body-sm text-gray-400">Closed</span>
              )}
            </div>
          ))}
        </div>
      </FieldGroup>
    </div>
  )
}

function HubCategoriesStep() {
  const { formState: { errors }, watch, setValue } = useFormContext<HubOnboardingData>()

  return (
    <div className="space-y-8">
      <CategorySelector
        label="Categories"
        options={[
          { value: 'arts-crafts', label: 'Arts & Crafts', icon: <PaletteIcon className="w-4 h-4" /> },
          { value: 'cooking', label: 'Cooking', icon: <ChefHatIcon className="w-4 h-4" /> },
          { value: 'wellness', label: 'Wellness', icon: <HeartIcon className="w-4 h-4" /> },
          { value: 'music', label: 'Music', icon: <MusicIcon className="w-4 h-4" /> },
          { value: 'technology', label: 'Technology', icon: <CodeIcon className="w-4 h-4" /> },
          { value: 'outdoor', label: 'Outdoor', icon: <TreesIcon className="w-4 h-4" /> },
          { value: 'fitness', label: 'Fitness', icon: <DumbbellIcon className="w-4 h-4" /> },
          { value: 'photography', label: 'Photography', icon: <CameraIcon className="w-4 h-4" /> },
        ]}
        value={watch('categories') || []}
        onChange={(value) => setValue('categories', value)}
        error={errors.categories?.message}
        required
      />

      <CategorySelector
        label="Target Audience"
        options={[
          { value: 'beginners', label: 'Beginners' },
          { value: 'intermediate', label: 'Intermediate' },
          { value: 'advanced', label: 'Advanced' },
          { value: 'kids', label: 'Kids (under 12)' },
          { value: 'teens', label: 'Teens (13-17)' },
          { value: 'adults', label: 'Adults (18+)' },
          { value: 'families', label: 'Families' },
          { value: 'corporate', label: 'Corporate Teams' },
        ]}
        value={watch('targetAudience') || []}
        onChange={(value) => setValue('targetAudience', value)}
        error={errors.targetAudience?.message}
        required
      />

      <CategorySelector
        label="Experience Types"
        options={[
          { value: 'workshop', label: 'Workshop' },
          { value: 'class', label: 'Class/Course' },
          { value: 'event', label: 'Event' },
          { value: 'retreat', label: 'Retreat' },
          { value: 'private', label: 'Private Session' },
          { value: 'team-building', label: 'Team Building' },
        ]}
        value={watch('experienceTypes') || []}
        onChange={(value) => setValue('experienceTypes', value)}
        error={errors.experienceTypes?.message}
        required
      />
    </div>
  )
}

function HubVerificationStep() {
  const { formState: { errors }, watch, setValue } = useFormContext<HubOnboardingData>()
  const hubType = watch('hubType')

  return (
    <div className="space-y-6">
      <div className="bg-blue-50 border border-blue-200 rounded-xl p-4">
        <div className="flex gap-3">
          <InfoIcon className="w-5 h-5 text-blue-500 flex-shrink-0 mt-0.5" />
          <div>
            <p className="text-body font-medium text-blue-800">
              Why verification?
            </p>
            <p className="text-body-sm text-blue-700 mt-1">
              Verification helps build trust with learners and ensures the safety of our community. Your documents are securely stored and never shared publicly.
            </p>
          </div>
        </div>
      </div>

      {hubType === 'organization' && (
        <FileUpload
          type="cover"
          label="Business Registration Document"
          value={watch('businessRegistration')}
          onChange={(file) => setValue('businessRegistration', file as File)}
          accept={['application/pdf', 'image/jpeg', 'image/png']}
          helperText="SSM registration, business license, or equivalent"
        />
      )}

      <FileUpload
        type="cover"
        label="Owner ID Document"
        value={watch('ownerIdDocument')}
        onChange={(file) => setValue('ownerIdDocument', file as File)}
        error={errors.ownerIdDocument?.message}
        accept={['application/pdf', 'image/jpeg', 'image/png']}
        helperText="MyKad, passport, or government-issued ID"
        required
      />

      <div className="pt-6 space-y-4">
        <Checkbox
          label="I agree to the Terms of Service and Privacy Policy"
          description="By checking this box, you agree to our terms and conditions."
          checked={watch('agreeToTerms')}
          onChange={(checked) => setValue('agreeToTerms', checked)}
          error={errors.agreeToTerms?.message}
        />

        <Checkbox
          label="I agree to the Host Guidelines"
          description="You commit to providing safe, quality experiences for learners."
          checked={watch('agreeToHostGuidelines')}
          onChange={(checked) => setValue('agreeToHostGuidelines', checked)}
          error={errors.agreeToHostGuidelines?.message}
        />
      </div>
    </div>
  )
}

// Main Hub Onboarding Form
export function HubOnboardingForm() {
  const [currentStep, setCurrentStep] = useState(0)
  const [isSubmitting, setIsSubmitting] = useState(false)
  const [isSuccess, setIsSuccess] = useState(false)

  const methods = useForm<HubOnboardingData>({
    resolver: zodResolver(hubOnboardingSchema),
    defaultValues: {
      hubType: 'individual',
      categories: [],
      targetAudience: [],
      experienceTypes: [],
      operatingHours: [],
      agreeToTerms: false,
      agreeToHostGuidelines: false,
    },
    mode: 'onBlur',
  })

  const stepSchemas = [
    hubBasicsSchema,
    hubLocationSchema,
    hubCategoriesSchema,
    hubVerificationSchema,
  ]

  const handleNext = async () => {
    const currentSchema = stepSchemas[currentStep]
    const currentFields = Object.keys(currentSchema.shape)
    const isValid = await methods.trigger(currentFields as any)

    if (isValid && currentStep < onboardingSteps.length - 1) {
      setCurrentStep((prev) => prev + 1)
      window.scrollTo({ top: 0, behavior: 'smooth' })
    }
  }

  const handleBack = () => {
    if (currentStep > 0) {
      setCurrentStep((prev) => prev - 1)
      window.scrollTo({ top: 0, behavior: 'smooth' })
    }
  }

  const handleSubmit = async (data: HubOnboardingData) => {
    setIsSubmitting(true)
    try {
      await createHub(data)
      setIsSuccess(true)
    } catch (error) {
      toast.error('Failed to create hub. Please try again.')
    } finally {
      setIsSubmitting(false)
    }
  }

  if (isSuccess) {
    return (
      <SuccessMessage
        title="Welcome to Mereka!"
        description="Your hub application has been submitted. We'll review it within 2-3 business days and notify you via email."
        action={{
          label: 'Go to Dashboard',
          onClick: () => router.push('/hub/dashboard'),
        }}
      />
    )
  }

  const stepComponents = [
    <HubBasicsStep key="basics" />,
    <HubLocationStep key="location" />,
    <HubCategoriesStep key="categories" />,
    <HubVerificationStep key="verification" />,
  ]

  return (
    <FormProvider {...methods}>
      <form onSubmit={methods.handleSubmit(handleSubmit)}>
        <MultiStepForm
          steps={onboardingSteps}
          currentStep={currentStep}
          onNext={handleNext}
          onBack={handleBack}
          onSubmit={handleSubmit}
          isSubmitting={isSubmitting}
        >
          {stepComponents[currentStep]}
        </MultiStepForm>
      </form>
    </FormProvider>
  )
}
```

---

## Accessibility Checklist

### Form-Level

- [ ] Use semantic `<form>` element
- [ ] Provide form title and description
- [ ] Include "required fields" legend
- [ ] Announce form submission status to screen readers
- [ ] Trap focus within modal forms

### Field-Level

- [ ] Every input has an associated `<label>`
- [ ] Use `aria-required` for required fields
- [ ] Use `aria-invalid` for fields with errors
- [ ] Link error messages with `aria-describedby`
- [ ] Announce errors with `role="alert"`

### Navigation

- [ ] Support keyboard navigation (Tab, Shift+Tab)
- [ ] Submit with Enter key
- [ ] Visible focus indicators
- [ ] Skip links for long forms

### Validation

- [ ] Don't rely on color alone for errors
- [ ] Provide clear, actionable error messages
- [ ] Focus first field with error on submit
- [ ] Validate on blur, not on every keystroke

---

## Do's and Don'ts

### Do's

- **Do** always provide labels for form fields
- **Do** use inline validation with clear error messages
- **Do** group related fields with fieldsets
- **Do** indicate required vs optional fields clearly
- **Do** provide helper text for complex fields
- **Do** disable submit button during submission
- **Do** show loading state during async operations
- **Do** provide confirmation after successful submission
- **Do** save form progress for multi-step forms
- **Do** use appropriate input types (email, tel, url)

### Don'ts

- **Don't** use placeholder text as the only label
- **Don't** show errors before user interaction
- **Don't** clear the form on validation error
- **Don't** use generic error messages
- **Don't** auto-submit without user confirmation
- **Don't** hide the submit button during loading
- **Don't** require unnecessary information
- **Don't** use all-caps for labels
- **Don't** disable browser autocomplete without reason
- **Don't** forget mobile keyboard considerations

---

## Related Documentation

- [Inputs](./inputs.md) - Individual input components
- [Buttons](./buttons.md) - Form action buttons
- [Patterns](./patterns.md) - Form patterns and validation
- [Components](./components.md) - UI component reference
