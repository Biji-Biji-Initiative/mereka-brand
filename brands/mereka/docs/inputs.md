# Inputs

> Mereka has **59 input variants** covering text fields, selects, date/time pickers, file uploads, checkboxes, radios, toggles, and category selectors.

**Figma Section**: Inputs

---

## Input Architecture

```
Form Field
├── Label (optional)
├── Input Container
│   ├── Leading Icon (optional)
│   ├── Input Element
│   └── Trailing Icon/Action (optional)
├── Helper Text (optional)
└── Error Message (conditional)
```

---

## Quick Reference

| Input Type | States | Variants |
|------------|--------|----------|
| **Text Input** | Default, Active, Filled, Error, Disabled | With/without icon, With/without label |
| **Select** | Default, Expanded, Hover, Selected, Multi, No results, Error, Disabled | Single, Multi-select |
| **Date Picker** | Enabled, Hover, Pressed, Active, Disabled, Range | Small, Medium |
| **Time Picker** | Default, Filled, Error, Disabled | Time, Duration |
| **File Upload** | Empty, Added | Cover photo, Gallery |
| **Checkbox** | Enabled, Hovered, Disabled | Selected, Unselected |
| **Radio** | Enabled, Hovered, Disabled | Selected, Unselected |
| **Toggle** | On, Off | Icons, Text |
| **Category Selector** | Default, Error, Selected | Type, Theme, Topic |

---

## 1. Text Input

The foundational input for collecting text data.

### States

| State | Visual | Behavior |
|-------|--------|----------|
| **Default** | Gray border, placeholder text | Ready for input |
| **Active/Focus** | Teal border, ring effect | Currently focused |
| **Filled** | Gray border, value displayed | Has content |
| **Error** | Red border, error message | Validation failed |
| **Disabled** | Gray background, muted text | Not interactive |

### Basic Text Input

```tsx
interface TextInputProps {
  label?: string
  placeholder?: string
  value?: string
  error?: string
  disabled?: boolean
  leadingIcon?: React.ReactNode
  trailingIcon?: React.ReactNode
}

function TextInput({
  label,
  placeholder,
  value,
  error,
  disabled,
  leadingIcon,
  trailingIcon,
  ...props
}: TextInputProps) {
  return (
    <div className="space-y-1.5">
      {label && (
        <label className="block font-body text-body-sm font-medium text-gray-700">
          {label}
        </label>
      )}
      <div className="relative">
        {leadingIcon && (
          <div className="absolute left-3 top-1/2 -translate-y-1/2 text-gray-400">
            {leadingIcon}
          </div>
        )}
        <input
          type="text"
          placeholder={placeholder}
          value={value}
          disabled={disabled}
          className={`
            w-full h-11 px-4 rounded-lg border font-body text-body
            transition-colors duration-150
            placeholder:text-gray-400
            focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal
            ${leadingIcon ? 'pl-10' : ''}
            ${trailingIcon ? 'pr-10' : ''}
            ${error
              ? 'border-red-500 focus:ring-red-500/20 focus:border-red-500'
              : 'border-gray-300'
            }
            ${disabled
              ? 'bg-gray-100 text-gray-400 cursor-not-allowed'
              : 'bg-white text-gray-900'
            }
          `}
          {...props}
        />
        {trailingIcon && (
          <div className="absolute right-3 top-1/2 -translate-y-1/2 text-gray-400">
            {trailingIcon}
          </div>
        )}
      </div>
      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### CVA Text Input

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const inputVariants = cva(
  'w-full h-11 px-4 rounded-lg border font-body text-body transition-colors duration-150 placeholder:text-gray-400 focus:outline-none focus:ring-2',
  {
    variants: {
      state: {
        default: 'border-gray-300 bg-white text-gray-900 focus:ring-mereka-teal/20 focus:border-mereka-teal',
        error: 'border-red-500 bg-white text-gray-900 focus:ring-red-500/20 focus:border-red-500',
        disabled: 'border-gray-200 bg-gray-100 text-gray-400 cursor-not-allowed',
      },
      hasLeadingIcon: {
        true: 'pl-10',
        false: '',
      },
      hasTrailingIcon: {
        true: 'pr-10',
        false: '',
      },
    },
    defaultVariants: {
      state: 'default',
      hasLeadingIcon: false,
      hasTrailingIcon: false,
    },
  }
)

export interface InputProps
  extends Omit<React.InputHTMLAttributes<HTMLInputElement>, 'size'>,
    VariantProps<typeof inputVariants> {
  label?: string
  error?: string
  leadingIcon?: React.ReactNode
  trailingIcon?: React.ReactNode
}

export function Input({
  label,
  error,
  leadingIcon,
  trailingIcon,
  disabled,
  className,
  ...props
}: InputProps) {
  const state = disabled ? 'disabled' : error ? 'error' : 'default'

  return (
    <div className="space-y-1.5">
      {label && (
        <label className="block font-body text-body-sm font-medium text-gray-700">
          {label}
        </label>
      )}
      <div className="relative">
        {leadingIcon && (
          <div className="absolute left-3 top-1/2 -translate-y-1/2 text-gray-400">
            {leadingIcon}
          </div>
        )}
        <input
          disabled={disabled}
          className={inputVariants({
            state,
            hasLeadingIcon: !!leadingIcon,
            hasTrailingIcon: !!trailingIcon,
            className,
          })}
          aria-invalid={!!error}
          aria-describedby={error ? `${props.id}-error` : undefined}
          {...props}
        />
        {trailingIcon && (
          <div className="absolute right-3 top-1/2 -translate-y-1/2 text-gray-400">
            {trailingIcon}
          </div>
        )}
      </div>
      {error && (
        <p
          id={`${props.id}-error`}
          className="flex items-center gap-1 text-caption text-red-600"
          role="alert"
        >
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Usage Examples

```tsx
// Basic input
<Input label="Full Name" placeholder="Enter your name" />

// With icon
<Input
  label="Email"
  type="email"
  placeholder="you@example.com"
  leadingIcon={<MailIcon className="w-5 h-5" />}
/>

// With error
<Input
  label="Username"
  value="ab"
  error="Username must be at least 3 characters"
/>

// Disabled
<Input label="Email" value="user@example.com" disabled />

// Search with trailing icon
<Input
  placeholder="Search experiences..."
  leadingIcon={<SearchIcon className="w-5 h-5" />}
  trailingIcon={
    <button className="hover:text-gray-600">
      <XIcon className="w-4 h-4" />
    </button>
  }
/>
```

---

## 2. Select / Dropdown

For single or multi-option selection.

### States

| State | Visual | Behavior |
|-------|--------|----------|
| **Default** | Gray border, placeholder | Ready to open |
| **Expanded** | Dropdown visible, chevron up | Options shown |
| **Expanded Hover** | Option highlighted | User browsing options |
| **Selected** | Value displayed | Single selection made |
| **Selected Multi** | Tags displayed | Multiple selections |
| **No Results** | Empty message | Search returned nothing |
| **Error** | Red border | Validation failed |
| **Disabled** | Gray background | Not interactive |

### Select Implementation

```tsx
import { useState, useRef, useEffect } from 'react'

interface SelectOption {
  value: string
  label: string
  disabled?: boolean
}

interface SelectProps {
  label?: string
  placeholder?: string
  options: SelectOption[]
  value?: string | string[]
  onChange?: (value: string | string[]) => void
  multiple?: boolean
  searchable?: boolean
  error?: string
  disabled?: boolean
}

function Select({
  label,
  placeholder = 'Select an option',
  options,
  value,
  onChange,
  multiple = false,
  searchable = false,
  error,
  disabled,
}: SelectProps) {
  const [isOpen, setIsOpen] = useState(false)
  const [search, setSearch] = useState('')
  const containerRef = useRef<HTMLDivElement>(null)

  const filteredOptions = searchable
    ? options.filter((opt) =>
        opt.label.toLowerCase().includes(search.toLowerCase())
      )
    : options

  const selectedOptions = multiple
    ? options.filter((opt) => (value as string[])?.includes(opt.value))
    : options.find((opt) => opt.value === value)

  return (
    <div className="space-y-1.5" ref={containerRef}>
      {label && (
        <label className="block font-body text-body-sm font-medium text-gray-700">
          {label}
        </label>
      )}

      <div className="relative">
        {/* Trigger */}
        <button
          type="button"
          disabled={disabled}
          onClick={() => !disabled && setIsOpen(!isOpen)}
          className={`
            w-full h-11 px-4 rounded-lg border text-left font-body text-body
            flex items-center justify-between gap-2
            transition-colors duration-150
            focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal
            ${error
              ? 'border-red-500'
              : isOpen
              ? 'border-mereka-teal ring-2 ring-mereka-teal/20'
              : 'border-gray-300'
            }
            ${disabled
              ? 'bg-gray-100 text-gray-400 cursor-not-allowed'
              : 'bg-white text-gray-900 cursor-pointer'
            }
          `}
          aria-haspopup="listbox"
          aria-expanded={isOpen}
        >
          <span className={!value || (Array.isArray(value) && value.length === 0) ? 'text-gray-400' : ''}>
            {multiple && Array.isArray(value) && value.length > 0 ? (
              <div className="flex flex-wrap gap-1">
                {selectedOptions.slice(0, 2).map((opt) => (
                  <span
                    key={opt.value}
                    className="inline-flex items-center gap-1 px-2 py-0.5 bg-gray-100 rounded text-body-sm"
                  >
                    {opt.label}
                    <XIcon className="w-3 h-3" />
                  </span>
                ))}
                {value.length > 2 && (
                  <span className="text-body-sm text-gray-500">
                    +{value.length - 2} more
                  </span>
                )}
              </div>
            ) : selectedOptions?.label || placeholder}
          </span>
          <ChevronDownIcon
            className={`w-5 h-5 text-gray-400 transition-transform ${isOpen ? 'rotate-180' : ''}`}
          />
        </button>

        {/* Dropdown */}
        {isOpen && (
          <div className="absolute z-50 w-full mt-1 bg-white border border-gray-200 rounded-lg shadow-lg overflow-hidden">
            {searchable && (
              <div className="p-2 border-b border-gray-100">
                <input
                  type="text"
                  placeholder="Search..."
                  value={search}
                  onChange={(e) => setSearch(e.target.value)}
                  className="w-full px-3 py-2 text-body-sm border border-gray-200 rounded-md focus:outline-none focus:border-mereka-teal"
                />
              </div>
            )}

            <ul className="max-h-60 overflow-auto py-1" role="listbox">
              {filteredOptions.length === 0 ? (
                <li className="px-4 py-3 text-body-sm text-gray-500 text-center">
                  No results found
                </li>
              ) : (
                filteredOptions.map((option) => {
                  const isSelected = multiple
                    ? (value as string[])?.includes(option.value)
                    : value === option.value

                  return (
                    <li
                      key={option.value}
                      role="option"
                      aria-selected={isSelected}
                      className={`
                        px-4 py-2.5 text-body-sm cursor-pointer
                        flex items-center justify-between
                        ${option.disabled ? 'opacity-50 cursor-not-allowed' : 'hover:bg-gray-50'}
                        ${isSelected ? 'bg-mereka-teal/5 text-mereka-teal' : 'text-gray-900'}
                      `}
                      onClick={() => {
                        if (option.disabled) return
                        if (multiple) {
                          const newValue = isSelected
                            ? (value as string[]).filter((v) => v !== option.value)
                            : [...(value as string[] || []), option.value]
                          onChange?.(newValue)
                        } else {
                          onChange?.(option.value)
                          setIsOpen(false)
                        }
                      }}
                    >
                      {option.label}
                      {isSelected && <CheckIcon className="w-4 h-4" />}
                    </li>
                  )
                })
              )}
            </ul>
          </div>
        )}
      </div>

      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600" role="alert">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Usage Examples

```tsx
// Basic select
<Select
  label="Category"
  placeholder="Select category"
  options={[
    { value: 'workshop', label: 'Workshop' },
    { value: 'course', label: 'Course' },
    { value: 'event', label: 'Event' },
  ]}
  value={category}
  onChange={setCategory}
/>

// Multi-select
<Select
  label="Tags"
  placeholder="Select tags"
  options={tagOptions}
  value={selectedTags}
  onChange={setSelectedTags}
  multiple
/>

// Searchable
<Select
  label="Location"
  placeholder="Search locations..."
  options={locations}
  value={location}
  onChange={setLocation}
  searchable
/>

// With error
<Select
  label="Experience Type"
  options={types}
  error="Please select an experience type"
/>
```

---

## 3. Date Picker

For selecting dates and date ranges.

### Sizes

| Size | Height | Usage |
|------|--------|-------|
| **Small** | 36px | Compact forms, inline |
| **Medium** | 44px | Standard forms |

### States

| State | Visual | Behavior |
|-------|--------|----------|
| **Enabled** | Default appearance | Ready for interaction |
| **Hover** | Subtle highlight | User hovering |
| **Pressed** | Darker highlight | Being clicked |
| **Active** | Calendar open | Selecting date |
| **Disabled** | Muted appearance | Not interactive |
| **Range Start** | Left-rounded highlight | First date selected |
| **Range End** | Right-rounded highlight | Last date selected |
| **Range Enabled** | Background between | Dates in range |
| **Range Empty** | No selection | Awaiting selection |

### Date Picker Implementation

```tsx
import { cva } from 'class-variance-authority'
import { useState } from 'react'

const datePickerVariants = cva(
  'w-full rounded-lg border font-body flex items-center gap-2 transition-colors duration-150 focus:outline-none focus:ring-2',
  {
    variants: {
      size: {
        small: 'h-9 px-3 text-body-sm',
        medium: 'h-11 px-4 text-body',
      },
      state: {
        enabled: 'border-gray-300 bg-white text-gray-900 hover:border-gray-400 focus:ring-mereka-teal/20 focus:border-mereka-teal cursor-pointer',
        disabled: 'border-gray-200 bg-gray-100 text-gray-400 cursor-not-allowed',
        error: 'border-red-500 bg-white text-gray-900 focus:ring-red-500/20 focus:border-red-500 cursor-pointer',
      },
    },
    defaultVariants: {
      size: 'medium',
      state: 'enabled',
    },
  }
)

interface DatePickerProps {
  label?: string
  placeholder?: string
  value?: Date | null
  onChange?: (date: Date | null) => void
  size?: 'small' | 'medium'
  disabled?: boolean
  error?: string
  minDate?: Date
  maxDate?: Date
  rangeMode?: boolean
  rangeValue?: { start: Date | null; end: Date | null }
  onRangeChange?: (range: { start: Date | null; end: Date | null }) => void
}

function DatePicker({
  label,
  placeholder = 'Select date',
  value,
  onChange,
  size = 'medium',
  disabled,
  error,
  minDate,
  maxDate,
  rangeMode = false,
  rangeValue,
  onRangeChange,
}: DatePickerProps) {
  const [isOpen, setIsOpen] = useState(false)
  const [currentMonth, setCurrentMonth] = useState(new Date())

  const state = disabled ? 'disabled' : error ? 'error' : 'enabled'

  const formatDate = (date: Date) => {
    return date.toLocaleDateString('en-MY', {
      day: 'numeric',
      month: 'short',
      year: 'numeric',
    })
  }

  const displayValue = rangeMode
    ? rangeValue?.start && rangeValue?.end
      ? `${formatDate(rangeValue.start)} - ${formatDate(rangeValue.end)}`
      : rangeValue?.start
      ? `${formatDate(rangeValue.start)} - Select end`
      : placeholder
    : value
    ? formatDate(value)
    : placeholder

  return (
    <div className="space-y-1.5">
      {label && (
        <label className="block font-body text-body-sm font-medium text-gray-700">
          {label}
        </label>
      )}

      <div className="relative">
        <button
          type="button"
          disabled={disabled}
          onClick={() => !disabled && setIsOpen(!isOpen)}
          className={datePickerVariants({ size, state })}
          aria-haspopup="dialog"
          aria-expanded={isOpen}
        >
          <CalendarIcon className="w-5 h-5 text-gray-400 shrink-0" />
          <span className={!value && !rangeValue?.start ? 'text-gray-400' : ''}>
            {displayValue}
          </span>
        </button>

        {isOpen && (
          <div className="absolute z-50 mt-1 p-4 bg-white border border-gray-200 rounded-xl shadow-lg">
            {/* Calendar Header */}
            <div className="flex items-center justify-between mb-4">
              <button
                type="button"
                onClick={() => setCurrentMonth(new Date(currentMonth.setMonth(currentMonth.getMonth() - 1)))}
                className="p-1 hover:bg-gray-100 rounded"
              >
                <ChevronLeftIcon className="w-5 h-5" />
              </button>
              <span className="font-heading text-h5">
                {currentMonth.toLocaleDateString('en-MY', { month: 'long', year: 'numeric' })}
              </span>
              <button
                type="button"
                onClick={() => setCurrentMonth(new Date(currentMonth.setMonth(currentMonth.getMonth() + 1)))}
                className="p-1 hover:bg-gray-100 rounded"
              >
                <ChevronRightIcon className="w-5 h-5" />
              </button>
            </div>

            {/* Weekday Headers */}
            <div className="grid grid-cols-7 gap-1 mb-2">
              {['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa'].map((day) => (
                <div key={day} className="w-9 h-9 flex items-center justify-center text-caption text-gray-400">
                  {day}
                </div>
              ))}
            </div>

            {/* Calendar Grid */}
            <div className="grid grid-cols-7 gap-1">
              {/* ... render calendar days ... */}
              {generateCalendarDays(currentMonth).map((day, index) => {
                const isSelected = value && isSameDay(day, value)
                const isRangeStart = rangeMode && rangeValue?.start && isSameDay(day, rangeValue.start)
                const isRangeEnd = rangeMode && rangeValue?.end && isSameDay(day, rangeValue.end)
                const isInRange = rangeMode && isDateInRange(day, rangeValue)
                const isDisabled = (minDate && day < minDate) || (maxDate && day > maxDate)

                return (
                  <button
                    key={index}
                    type="button"
                    disabled={isDisabled}
                    onClick={() => {
                      if (rangeMode) {
                        // Handle range selection
                      } else {
                        onChange?.(day)
                        setIsOpen(false)
                      }
                    }}
                    className={`
                      w-9 h-9 flex items-center justify-center text-body-sm rounded-lg
                      transition-colors
                      ${isDisabled ? 'text-gray-300 cursor-not-allowed' : 'hover:bg-gray-100'}
                      ${isSelected ? 'bg-mereka-teal text-white' : ''}
                      ${isRangeStart ? 'bg-mereka-teal text-white rounded-r-none' : ''}
                      ${isRangeEnd ? 'bg-mereka-teal text-white rounded-l-none' : ''}
                      ${isInRange && !isRangeStart && !isRangeEnd ? 'bg-mereka-teal/10 rounded-none' : ''}
                    `}
                  >
                    {day.getDate()}
                  </button>
                )
              })}
            </div>
          </div>
        )}
      </div>

      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600" role="alert">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Usage Examples

```tsx
// Basic date picker
<DatePicker
  label="Event Date"
  value={eventDate}
  onChange={setEventDate}
/>

// Small size
<DatePicker
  size="small"
  placeholder="Pick a date"
  value={date}
  onChange={setDate}
/>

// With constraints
<DatePicker
  label="Booking Date"
  value={bookingDate}
  onChange={setBookingDate}
  minDate={new Date()}
  maxDate={addMonths(new Date(), 3)}
/>

// Date range
<DatePicker
  label="Date Range"
  rangeMode
  rangeValue={dateRange}
  onRangeChange={setDateRange}
  placeholder="Select date range"
/>

// Disabled
<DatePicker
  label="Start Date"
  value={new Date()}
  disabled
/>
```

---

## 4. Time Picker

For selecting times and durations.

### Types

| Type | Usage | Format |
|------|-------|--------|
| **Time** | Specific time selection | HH:MM AM/PM |
| **Duration** | Length of time | X hours Y minutes |

### States

| State | Visual | Behavior |
|-------|--------|----------|
| **Default** | Placeholder shown | Ready for input |
| **Filled** | Time value displayed | Selection made |
| **Error** | Red border | Validation failed |
| **Disabled** | Muted appearance | Not interactive |

### Time Picker Implementation

```tsx
import { cva } from 'class-variance-authority'

const timePickerVariants = cva(
  'w-full h-11 px-4 rounded-lg border font-body text-body flex items-center gap-2 transition-colors duration-150 focus:outline-none focus:ring-2',
  {
    variants: {
      state: {
        default: 'border-gray-300 bg-white text-gray-900 focus:ring-mereka-teal/20 focus:border-mereka-teal cursor-pointer',
        filled: 'border-gray-300 bg-white text-gray-900 focus:ring-mereka-teal/20 focus:border-mereka-teal cursor-pointer',
        error: 'border-red-500 bg-white text-gray-900 focus:ring-red-500/20 focus:border-red-500 cursor-pointer',
        disabled: 'border-gray-200 bg-gray-100 text-gray-400 cursor-not-allowed',
      },
    },
    defaultVariants: {
      state: 'default',
    },
  }
)

interface TimePickerProps {
  label?: string
  type?: 'time' | 'duration'
  value?: string
  onChange?: (value: string) => void
  placeholder?: string
  error?: string
  disabled?: boolean
  minTime?: string
  maxTime?: string
}

function TimePicker({
  label,
  type = 'time',
  value,
  onChange,
  placeholder,
  error,
  disabled,
  minTime,
  maxTime,
}: TimePickerProps) {
  const [isOpen, setIsOpen] = useState(false)
  const [hours, setHours] = useState(12)
  const [minutes, setMinutes] = useState(0)
  const [period, setPeriod] = useState<'AM' | 'PM'>('AM')

  const state = disabled ? 'disabled' : error ? 'error' : value ? 'filled' : 'default'

  const defaultPlaceholder = type === 'time' ? 'Select time' : 'Select duration'

  const formatTime = () => {
    if (type === 'time') {
      return `${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')} ${period}`
    } else {
      return `${hours} hr${hours !== 1 ? 's' : ''} ${minutes} min`
    }
  }

  return (
    <div className="space-y-1.5">
      {label && (
        <label className="block font-body text-body-sm font-medium text-gray-700">
          {label}
        </label>
      )}

      <div className="relative">
        <button
          type="button"
          disabled={disabled}
          onClick={() => !disabled && setIsOpen(!isOpen)}
          className={timePickerVariants({ state })}
          aria-haspopup="dialog"
          aria-expanded={isOpen}
        >
          <ClockIcon className="w-5 h-5 text-gray-400 shrink-0" />
          <span className={!value ? 'text-gray-400' : ''}>
            {value || placeholder || defaultPlaceholder}
          </span>
        </button>

        {isOpen && (
          <div className="absolute z-50 mt-1 p-4 bg-white border border-gray-200 rounded-xl shadow-lg min-w-[200px]">
            <div className="flex items-center justify-center gap-2">
              {/* Hours */}
              <div className="flex flex-col items-center">
                <button
                  type="button"
                  onClick={() => setHours((h) => (type === 'time' ? (h % 12) + 1 : Math.min(h + 1, 23)))}
                  className="p-1 hover:bg-gray-100 rounded"
                >
                  <ChevronUpIcon className="w-5 h-5" />
                </button>
                <span className="w-12 text-center text-h3 font-heading py-2">
                  {hours.toString().padStart(2, '0')}
                </span>
                <button
                  type="button"
                  onClick={() => setHours((h) => (type === 'time' ? ((h - 2 + 12) % 12) + 1 : Math.max(h - 1, 0)))}
                  className="p-1 hover:bg-gray-100 rounded"
                >
                  <ChevronDownIcon className="w-5 h-5" />
                </button>
              </div>

              <span className="text-h3 font-heading">:</span>

              {/* Minutes */}
              <div className="flex flex-col items-center">
                <button
                  type="button"
                  onClick={() => setMinutes((m) => (m + 5) % 60)}
                  className="p-1 hover:bg-gray-100 rounded"
                >
                  <ChevronUpIcon className="w-5 h-5" />
                </button>
                <span className="w-12 text-center text-h3 font-heading py-2">
                  {minutes.toString().padStart(2, '0')}
                </span>
                <button
                  type="button"
                  onClick={() => setMinutes((m) => (m - 5 + 60) % 60)}
                  className="p-1 hover:bg-gray-100 rounded"
                >
                  <ChevronDownIcon className="w-5 h-5" />
                </button>
              </div>

              {/* AM/PM toggle for time type */}
              {type === 'time' && (
                <div className="flex flex-col gap-1 ml-2">
                  <button
                    type="button"
                    onClick={() => setPeriod('AM')}
                    className={`px-3 py-1 rounded text-body-sm ${
                      period === 'AM' ? 'bg-mereka-teal text-white' : 'hover:bg-gray-100'
                    }`}
                  >
                    AM
                  </button>
                  <button
                    type="button"
                    onClick={() => setPeriod('PM')}
                    className={`px-3 py-1 rounded text-body-sm ${
                      period === 'PM' ? 'bg-mereka-teal text-white' : 'hover:bg-gray-100'
                    }`}
                  >
                    PM
                  </button>
                </div>
              )}
            </div>

            <button
              type="button"
              onClick={() => {
                onChange?.(formatTime())
                setIsOpen(false)
              }}
              className="w-full mt-4 py-2 bg-gray-900 text-white rounded-lg text-body-sm hover:bg-gray-800"
            >
              Confirm
            </button>
          </div>
        )}
      </div>

      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600" role="alert">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Usage Examples

```tsx
// Time selection
<TimePicker
  label="Start Time"
  type="time"
  value={startTime}
  onChange={setStartTime}
/>

// Duration
<TimePicker
  label="Session Duration"
  type="duration"
  value={duration}
  onChange={setDuration}
  placeholder="How long?"
/>

// With error
<TimePicker
  label="End Time"
  type="time"
  error="End time must be after start time"
/>

// Disabled
<TimePicker
  label="Time"
  value="09:00 AM"
  disabled
/>
```

---

## 5. File Upload

For uploading images and documents.

### Types

| Type | Usage | Behavior |
|------|-------|----------|
| **Cover Photo** | Single hero image | Replace on new upload |
| **Gallery** | Multiple images | Add/remove individual |

### States

| State | Visual | Behavior |
|-------|--------|----------|
| **Empty (Added: No)** | Upload prompt | Ready for file |
| **Filled (Added: Yes)** | Preview shown | File uploaded |

### File Upload Implementation

```tsx
import { useCallback, useState } from 'react'
import { useDropzone } from 'react-dropzone'

interface FileUploadProps {
  type?: 'cover' | 'gallery'
  label?: string
  value?: File | File[] | null
  onChange?: (files: File | File[] | null) => void
  accept?: string[]
  maxSize?: number // bytes
  maxFiles?: number
  error?: string
}

function FileUpload({
  type = 'cover',
  label,
  value,
  onChange,
  accept = ['image/jpeg', 'image/png', 'image/webp'],
  maxSize = 5 * 1024 * 1024, // 5MB
  maxFiles = 10,
  error,
}: FileUploadProps) {
  const [previews, setPreviews] = useState<string[]>([])

  const onDrop = useCallback((acceptedFiles: File[]) => {
    if (type === 'cover') {
      const file = acceptedFiles[0]
      onChange?.(file)
      setPreviews([URL.createObjectURL(file)])
    } else {
      const newFiles = [...((value as File[]) || []), ...acceptedFiles].slice(0, maxFiles)
      onChange?.(newFiles)
      setPreviews(newFiles.map((f) => URL.createObjectURL(f)))
    }
  }, [type, value, onChange, maxFiles])

  const { getRootProps, getInputProps, isDragActive } = useDropzone({
    onDrop,
    accept: accept.reduce((acc, type) => ({ ...acc, [type]: [] }), {}),
    maxSize,
    multiple: type === 'gallery',
  })

  const removeFile = (index: number) => {
    if (type === 'cover') {
      onChange?.(null)
      setPreviews([])
    } else {
      const newFiles = (value as File[]).filter((_, i) => i !== index)
      onChange?.(newFiles.length > 0 ? newFiles : null)
      setPreviews(previews.filter((_, i) => i !== index))
    }
  }

  const hasFiles = type === 'cover' ? !!value : (value as File[])?.length > 0

  return (
    <div className="space-y-1.5">
      {label && (
        <label className="block font-body text-body-sm font-medium text-gray-700">
          {label}
        </label>
      )}

      {/* Cover Photo */}
      {type === 'cover' && (
        <div
          {...getRootProps()}
          className={`
            relative aspect-video rounded-xl border-2 border-dashed overflow-hidden
            transition-colors cursor-pointer
            ${isDragActive ? 'border-mereka-teal bg-mereka-teal/5' : 'border-gray-300'}
            ${error ? 'border-red-500' : ''}
            ${hasFiles ? 'border-solid border-gray-200' : 'hover:border-gray-400'}
          `}
        >
          <input {...getInputProps()} aria-label={label || 'Upload cover photo'} />

          {hasFiles ? (
            <>
              <img
                src={previews[0]}
                alt="Cover preview"
                className="w-full h-full object-cover"
              />
              <div className="absolute inset-0 bg-black/40 opacity-0 hover:opacity-100 transition-opacity flex items-center justify-center gap-2">
                <button
                  type="button"
                  onClick={(e) => {
                    e.stopPropagation()
                    removeFile(0)
                  }}
                  className="p-2 bg-white rounded-full text-gray-700 hover:bg-gray-100"
                >
                  <TrashIcon className="w-5 h-5" />
                </button>
              </div>
            </>
          ) : (
            <div className="absolute inset-0 flex flex-col items-center justify-center text-gray-400">
              <UploadCloudIcon className="w-10 h-10 mb-2" />
              <p className="text-body-sm font-medium">
                {isDragActive ? 'Drop image here' : 'Drag & drop or click to upload'}
              </p>
              <p className="text-caption mt-1">PNG, JPG up to {maxSize / 1024 / 1024}MB</p>
            </div>
          )}
        </div>
      )}

      {/* Gallery */}
      {type === 'gallery' && (
        <div className="space-y-3">
          {/* Upload zone */}
          <div
            {...getRootProps()}
            className={`
              p-6 rounded-xl border-2 border-dashed text-center
              transition-colors cursor-pointer
              ${isDragActive ? 'border-mereka-teal bg-mereka-teal/5' : 'border-gray-300 hover:border-gray-400'}
              ${error ? 'border-red-500' : ''}
            `}
          >
            <input {...getInputProps()} aria-label={label || 'Upload gallery images'} />
            <UploadCloudIcon className="w-8 h-8 mx-auto mb-2 text-gray-400" />
            <p className="text-body-sm font-medium text-gray-600">
              {isDragActive ? 'Drop images here' : 'Add photos'}
            </p>
            <p className="text-caption text-gray-400 mt-1">
              Up to {maxFiles} images, {maxSize / 1024 / 1024}MB each
            </p>
          </div>

          {/* Preview grid */}
          {hasFiles && (
            <div className="grid grid-cols-4 gap-3">
              {previews.map((preview, index) => (
                <div key={index} className="relative aspect-square rounded-lg overflow-hidden group">
                  <img
                    src={preview}
                    alt={`Gallery image ${index + 1}`}
                    className="w-full h-full object-cover"
                  />
                  <button
                    type="button"
                    onClick={() => removeFile(index)}
                    className="absolute top-2 right-2 p-1.5 bg-white rounded-full shadow-md opacity-0 group-hover:opacity-100 transition-opacity"
                  >
                    <XIcon className="w-4 h-4 text-gray-600" />
                  </button>
                </div>
              ))}
            </div>
          )}
        </div>
      )}

      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600" role="alert">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Usage Examples

```tsx
// Cover photo
<FileUpload
  type="cover"
  label="Cover Image"
  value={coverImage}
  onChange={setCoverImage}
/>

// Gallery
<FileUpload
  type="gallery"
  label="Experience Photos"
  value={galleryImages}
  onChange={setGalleryImages}
  maxFiles={8}
/>

// With error
<FileUpload
  type="cover"
  label="Profile Picture"
  error="Please upload an image"
/>
```

---

## 6. Checkbox

For binary or multi-select choices.

### States

| State | Visual | Behavior |
|-------|--------|----------|
| **Enabled** | Default appearance | Interactive |
| **Hovered** | Subtle highlight | Mouse over |
| **Disabled** | Muted appearance | Not interactive |

### Selection States

| Selected | Visual |
|----------|--------|
| **Yes** | Teal background, checkmark |
| **No** | Empty box, gray border |

### Checkbox Implementation

```tsx
import { cva } from 'class-variance-authority'

const checkboxVariants = cva(
  'w-5 h-5 rounded border-2 flex items-center justify-center transition-colors shrink-0',
  {
    variants: {
      state: {
        enabled: 'border-gray-300 hover:border-gray-400 cursor-pointer',
        disabled: 'border-gray-200 bg-gray-100 cursor-not-allowed',
      },
      checked: {
        true: 'bg-mereka-teal border-mereka-teal',
        false: 'bg-white',
      },
    },
    compoundVariants: [
      {
        state: 'disabled',
        checked: true,
        className: 'bg-gray-300 border-gray-300',
      },
    ],
    defaultVariants: {
      state: 'enabled',
      checked: false,
    },
  }
)

interface CheckboxProps {
  label?: string
  description?: string
  checked?: boolean
  onChange?: (checked: boolean) => void
  disabled?: boolean
  error?: string
}

function Checkbox({
  label,
  description,
  checked = false,
  onChange,
  disabled,
  error,
}: CheckboxProps) {
  const state = disabled ? 'disabled' : 'enabled'

  return (
    <div className="space-y-1">
      <label
        className={`flex items-start gap-3 ${disabled ? 'cursor-not-allowed' : 'cursor-pointer'}`}
      >
        <button
          type="button"
          role="checkbox"
          aria-checked={checked}
          disabled={disabled}
          onClick={() => !disabled && onChange?.(!checked)}
          className={checkboxVariants({ state, checked })}
        >
          {checked && (
            <CheckIcon className={`w-3 h-3 ${disabled ? 'text-gray-500' : 'text-white'}`} />
          )}
        </button>
        <div className="flex-1">
          {label && (
            <span className={`font-body text-body ${disabled ? 'text-gray-400' : 'text-gray-900'}`}>
              {label}
            </span>
          )}
          {description && (
            <p className="font-body text-body-sm text-gray-500 mt-0.5">
              {description}
            </p>
          )}
        </div>
      </label>
      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600 ml-8" role="alert">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </div>
  )
}
```

### Checkbox Group

```tsx
interface CheckboxGroupProps {
  label?: string
  options: { value: string; label: string; description?: string }[]
  value: string[]
  onChange: (value: string[]) => void
  error?: string
}

function CheckboxGroup({
  label,
  options,
  value,
  onChange,
  error,
}: CheckboxGroupProps) {
  const handleChange = (optionValue: string, checked: boolean) => {
    if (checked) {
      onChange([...value, optionValue])
    } else {
      onChange(value.filter((v) => v !== optionValue))
    }
  }

  return (
    <fieldset className="space-y-3">
      {label && (
        <legend className="font-body text-body-sm font-medium text-gray-700 mb-2">
          {label}
        </legend>
      )}
      {options.map((option) => (
        <Checkbox
          key={option.value}
          label={option.label}
          description={option.description}
          checked={value.includes(option.value)}
          onChange={(checked) => handleChange(option.value, checked)}
        />
      ))}
      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600" role="alert">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </fieldset>
  )
}
```

### Usage Examples

```tsx
// Single checkbox
<Checkbox
  label="I agree to the Terms of Service"
  checked={agreed}
  onChange={setAgreed}
/>

// With description
<Checkbox
  label="Email notifications"
  description="Receive updates about bookings and new experiences"
  checked={emailNotifications}
  onChange={setEmailNotifications}
/>

// Disabled
<Checkbox
  label="Premium features"
  description="Upgrade your plan to enable"
  checked={false}
  disabled
/>

// Checkbox group
<CheckboxGroup
  label="What are you interested in?"
  options={[
    { value: 'workshops', label: 'Workshops' },
    { value: 'courses', label: 'Courses' },
    { value: 'events', label: 'Events' },
  ]}
  value={interests}
  onChange={setInterests}
/>
```

---

## 7. Radio Button

For single-select choices from a group.

### States

| State | Visual | Behavior |
|-------|--------|----------|
| **Enabled** | Default appearance | Interactive |
| **Hovered** | Subtle highlight | Mouse over |
| **Disabled** | Muted appearance | Not interactive |

### Selection States

| Selected | Visual |
|----------|--------|
| **Yes** | Filled teal circle |
| **No** | Empty circle |

### Radio Implementation

```tsx
const radioVariants = cva(
  'w-5 h-5 rounded-full border-2 flex items-center justify-center transition-colors shrink-0',
  {
    variants: {
      state: {
        enabled: 'border-gray-300 hover:border-gray-400 cursor-pointer',
        disabled: 'border-gray-200 bg-gray-100 cursor-not-allowed',
      },
      checked: {
        true: 'border-mereka-teal',
        false: 'bg-white',
      },
    },
    compoundVariants: [
      {
        state: 'disabled',
        checked: true,
        className: 'border-gray-300',
      },
    ],
    defaultVariants: {
      state: 'enabled',
      checked: false,
    },
  }
)

interface RadioOption {
  value: string
  label: string
  description?: string
  disabled?: boolean
}

interface RadioGroupProps {
  label?: string
  name: string
  options: RadioOption[]
  value?: string
  onChange?: (value: string) => void
  error?: string
  orientation?: 'vertical' | 'horizontal'
}

function RadioGroup({
  label,
  name,
  options,
  value,
  onChange,
  error,
  orientation = 'vertical',
}: RadioGroupProps) {
  return (
    <fieldset className="space-y-2">
      {label && (
        <legend className="font-body text-body-sm font-medium text-gray-700 mb-3">
          {label}
        </legend>
      )}
      <div className={`flex ${orientation === 'vertical' ? 'flex-col gap-3' : 'flex-row gap-6'}`}>
        {options.map((option) => {
          const isChecked = value === option.value
          const state = option.disabled ? 'disabled' : 'enabled'

          return (
            <label
              key={option.value}
              className={`flex items-start gap-3 ${option.disabled ? 'cursor-not-allowed' : 'cursor-pointer'}`}
            >
              <button
                type="button"
                role="radio"
                name={name}
                aria-checked={isChecked}
                disabled={option.disabled}
                onClick={() => !option.disabled && onChange?.(option.value)}
                className={radioVariants({ state, checked: isChecked })}
              >
                {isChecked && (
                  <div className={`w-2.5 h-2.5 rounded-full ${option.disabled ? 'bg-gray-400' : 'bg-mereka-teal'}`} />
                )}
              </button>
              <div className="flex-1">
                <span className={`font-body text-body ${option.disabled ? 'text-gray-400' : 'text-gray-900'}`}>
                  {option.label}
                </span>
                {option.description && (
                  <p className="font-body text-body-sm text-gray-500 mt-0.5">
                    {option.description}
                  </p>
                )}
              </div>
            </label>
          )
        })}
      </div>
      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600" role="alert">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </fieldset>
  )
}
```

### Usage Examples

```tsx
// Basic radio group
<RadioGroup
  label="Experience Type"
  name="type"
  options={[
    { value: 'physical', label: 'In-person' },
    { value: 'virtual', label: 'Online' },
    { value: 'hybrid', label: 'Hybrid' },
  ]}
  value={experienceType}
  onChange={setExperienceType}
/>

// With descriptions
<RadioGroup
  label="Pricing Model"
  name="pricing"
  options={[
    {
      value: 'per-person',
      label: 'Per person',
      description: 'Charge each attendee individually',
    },
    {
      value: 'per-session',
      label: 'Per session',
      description: 'Fixed price regardless of attendees',
    },
  ]}
  value={pricingModel}
  onChange={setPricingModel}
/>

// Horizontal layout
<RadioGroup
  label="Sort by"
  name="sort"
  orientation="horizontal"
  options={[
    { value: 'date', label: 'Date' },
    { value: 'price', label: 'Price' },
    { value: 'rating', label: 'Rating' },
  ]}
  value={sortBy}
  onChange={setSortBy}
/>

// With disabled option
<RadioGroup
  label="Plan"
  name="plan"
  options={[
    { value: 'basic', label: 'Basic' },
    { value: 'pro', label: 'Pro', disabled: true },
    { value: 'enterprise', label: 'Enterprise' },
  ]}
  value={plan}
  onChange={setPlan}
/>
```

---

## 8. Toggle / Switch

For on/off settings.

### Styles

| Style | Visual |
|-------|--------|
| **Icons** | Sun/moon or similar icons |
| **Text** | ON/OFF labels |

### States

| State | Visual |
|-------|--------|
| **On** | Teal background, knob right |
| **Off** | Gray background, knob left |

### Toggle Implementation

```tsx
import { cva } from 'class-variance-authority'

const toggleTrackVariants = cva(
  'relative inline-flex items-center rounded-full transition-colors duration-200 focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:ring-offset-2',
  {
    variants: {
      size: {
        default: 'w-11 h-6',
        large: 'w-14 h-8',
      },
      checked: {
        true: 'bg-mereka-teal',
        false: 'bg-gray-300',
      },
      disabled: {
        true: 'opacity-50 cursor-not-allowed',
        false: 'cursor-pointer',
      },
    },
    defaultVariants: {
      size: 'default',
      checked: false,
      disabled: false,
    },
  }
)

const toggleKnobVariants = cva(
  'absolute bg-white rounded-full shadow-md transform transition-transform duration-200',
  {
    variants: {
      size: {
        default: 'w-5 h-5 top-0.5',
        large: 'w-7 h-7 top-0.5',
      },
      checked: {
        true: '',
        false: 'left-0.5',
      },
    },
    compoundVariants: [
      { size: 'default', checked: true, className: 'left-[22px]' },
      { size: 'large', checked: true, className: 'left-[26px]' },
    ],
    defaultVariants: {
      size: 'default',
      checked: false,
    },
  }
)

interface ToggleProps {
  label?: string
  description?: string
  checked?: boolean
  onChange?: (checked: boolean) => void
  disabled?: boolean
  size?: 'default' | 'large'
  style?: 'default' | 'icons' | 'text'
}

function Toggle({
  label,
  description,
  checked = false,
  onChange,
  disabled,
  size = 'default',
  style = 'default',
}: ToggleProps) {
  return (
    <div className="flex items-start justify-between gap-4">
      {(label || description) && (
        <div className="flex-1">
          {label && (
            <span className={`font-body text-body ${disabled ? 'text-gray-400' : 'text-gray-900'}`}>
              {label}
            </span>
          )}
          {description && (
            <p className="font-body text-body-sm text-gray-500 mt-0.5">
              {description}
            </p>
          )}
        </div>
      )}

      <button
        type="button"
        role="switch"
        aria-checked={checked}
        disabled={disabled}
        onClick={() => !disabled && onChange?.(!checked)}
        className={toggleTrackVariants({ size, checked, disabled })}
      >
        {/* Icons style */}
        {style === 'icons' && (
          <>
            <SunIcon className={`absolute left-1.5 w-3 h-3 ${checked ? 'text-white/50' : 'text-yellow-500'}`} />
            <MoonIcon className={`absolute right-1.5 w-3 h-3 ${checked ? 'text-white' : 'text-gray-400'}`} />
          </>
        )}

        {/* Text style */}
        {style === 'text' && (
          <>
            <span className={`absolute left-1 text-[10px] font-bold ${checked ? 'text-white' : 'text-transparent'}`}>
              ON
            </span>
            <span className={`absolute right-1 text-[10px] font-bold ${checked ? 'text-transparent' : 'text-gray-500'}`}>
              OFF
            </span>
          </>
        )}

        {/* Knob */}
        <span className={toggleKnobVariants({ size, checked })} />
      </button>
    </div>
  )
}
```

### Usage Examples

```tsx
// Basic toggle
<Toggle
  label="Dark mode"
  checked={darkMode}
  onChange={setDarkMode}
/>

// With description
<Toggle
  label="Push notifications"
  description="Receive alerts for new bookings and messages"
  checked={pushEnabled}
  onChange={setPushEnabled}
/>

// With icons
<Toggle
  label="Theme"
  style="icons"
  checked={isDark}
  onChange={setIsDark}
/>

// With text
<Toggle
  label="Auto-save"
  style="text"
  checked={autoSave}
  onChange={setAutoSave}
/>

// Disabled
<Toggle
  label="Premium feature"
  checked={false}
  disabled
/>

// Large size
<Toggle
  label="Visibility"
  size="large"
  checked={isVisible}
  onChange={setIsVisible}
/>
```

---

## 9. Category / Chip Selector

For selecting categories, themes, and topics.

### Types

| Type | Usage | Visual |
|------|-------|--------|
| **Type** | Experience classification | Pill chips |
| **Theme** | Learning themes | Icon + label |
| **Topic** | Content topics | Tag-style |

### States

| State | Visual | Behavior |
|-------|--------|----------|
| **Default** | Unselected appearance | Ready to select |
| **Error** | Red border/message | Validation failed |
| **Selected/Added** | Highlighted, checkmark | Item selected |

### Category Selector Implementation

```tsx
import { cva } from 'class-variance-authority'

const chipVariants = cva(
  'inline-flex items-center gap-2 px-4 py-2 rounded-full border transition-colors duration-150 cursor-pointer font-body text-body-sm',
  {
    variants: {
      selected: {
        true: 'bg-mereka-teal border-mereka-teal text-white',
        false: 'bg-white border-gray-300 text-gray-700 hover:border-gray-400 hover:bg-gray-50',
      },
      hasError: {
        true: 'border-red-500',
        false: '',
      },
    },
    defaultVariants: {
      selected: false,
      hasError: false,
    },
  }
)

interface CategoryOption {
  value: string
  label: string
  icon?: React.ReactNode
  count?: number
}

interface CategorySelectorProps {
  label?: string
  type?: 'type' | 'theme' | 'topic'
  options: CategoryOption[]
  value: string[]
  onChange: (value: string[]) => void
  error?: string
  maxSelections?: number
  required?: boolean
}

function CategorySelector({
  label,
  type = 'type',
  options,
  value,
  onChange,
  error,
  maxSelections,
  required,
}: CategorySelectorProps) {
  const handleToggle = (optionValue: string) => {
    const isSelected = value.includes(optionValue)

    if (isSelected) {
      onChange(value.filter((v) => v !== optionValue))
    } else {
      if (maxSelections && value.length >= maxSelections) {
        return // Don't add if at max
      }
      onChange([...value, optionValue])
    }
  }

  return (
    <fieldset className="space-y-3">
      {label && (
        <legend className="font-body text-body-sm font-medium text-gray-700">
          {label}
          {required && <span className="text-red-500 ml-1">*</span>}
          {maxSelections && (
            <span className="font-normal text-gray-400 ml-2">
              (select up to {maxSelections})
            </span>
          )}
        </legend>
      )}

      <div className="flex flex-wrap gap-2" role="group" aria-label={label}>
        {options.map((option) => {
          const isSelected = value.includes(option.value)
          const isDisabled = !isSelected && maxSelections && value.length >= maxSelections

          return (
            <button
              key={option.value}
              type="button"
              onClick={() => !isDisabled && handleToggle(option.value)}
              disabled={isDisabled}
              aria-pressed={isSelected}
              className={chipVariants({
                selected: isSelected,
                hasError: !!error && !isSelected,
                className: isDisabled ? 'opacity-50 cursor-not-allowed' : '',
              })}
            >
              {option.icon && (
                <span className={isSelected ? 'text-white' : 'text-gray-500'}>
                  {option.icon}
                </span>
              )}
              {option.label}
              {isSelected && <CheckIcon className="w-4 h-4" />}
              {option.count !== undefined && (
                <span className={`text-caption ${isSelected ? 'text-white/70' : 'text-gray-400'}`}>
                  ({option.count})
                </span>
              )}
            </button>
          )
        })}
      </div>

      {error && (
        <p className="flex items-center gap-1 text-caption text-red-600" role="alert">
          <AlertCircleIcon className="w-3.5 h-3.5" />
          {error}
        </p>
      )}
    </fieldset>
  )
}
```

### Usage Examples

```tsx
// Experience types
<CategorySelector
  label="Experience Type"
  type="type"
  options={[
    { value: 'workshop', label: 'Workshop' },
    { value: 'course', label: 'Course' },
    { value: 'event', label: 'Event' },
    { value: 'retreat', label: 'Retreat' },
  ]}
  value={selectedTypes}
  onChange={setSelectedTypes}
  required
/>

// Themes with icons
<CategorySelector
  label="Learning Themes"
  type="theme"
  options={[
    { value: 'art', label: 'Art & Craft', icon: <PaletteIcon className="w-4 h-4" /> },
    { value: 'cooking', label: 'Cooking', icon: <ChefHatIcon className="w-4 h-4" /> },
    { value: 'music', label: 'Music', icon: <MusicIcon className="w-4 h-4" /> },
    { value: 'wellness', label: 'Wellness', icon: <HeartIcon className="w-4 h-4" /> },
  ]}
  value={selectedThemes}
  onChange={setSelectedThemes}
  maxSelections={3}
/>

// Topics with counts
<CategorySelector
  label="Topics"
  type="topic"
  options={[
    { value: 'beginner', label: 'Beginner', count: 42 },
    { value: 'intermediate', label: 'Intermediate', count: 28 },
    { value: 'advanced', label: 'Advanced', count: 15 },
  ]}
  value={selectedTopics}
  onChange={setSelectedTopics}
/>

// With error
<CategorySelector
  label="Select at least one category"
  options={categories}
  value={[]}
  onChange={setCategories}
  error="Please select at least one category"
  required
/>
```

---

## Accessibility Notes

### General Guidelines

- All inputs must have associated labels (visible or `aria-label`)
- Error messages should be linked via `aria-describedby`
- Use `aria-invalid="true"` on inputs with errors
- Ensure sufficient color contrast (4.5:1 for text)
- Support keyboard navigation (Tab, Enter, Space, Arrow keys)
- Announce state changes to screen readers

### Focus Management

```tsx
// All interactive inputs should have visible focus states
className="focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal"

// Skip to main content for long forms
<a href="#main-form" className="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 bg-white px-4 py-2 rounded-lg shadow-lg">
  Skip to form
</a>
```

### Required Fields

```tsx
// Mark required fields clearly
<label>
  Email
  <span className="text-red-500 ml-1" aria-hidden="true">*</span>
  <span className="sr-only">(required)</span>
</label>
```

### Error Handling

```tsx
// Announce errors to screen readers
<div role="alert" aria-live="polite">
  {errors.map((error) => (
    <p key={error.field} className="text-red-600">
      {error.message}
    </p>
  ))}
</div>
```

---

## Validation Patterns

### Client-Side Validation

```tsx
import { z } from 'zod'

// Email validation
const emailSchema = z.string().email('Please enter a valid email address')

// Required field
const requiredSchema = z.string().min(1, 'This field is required')

// Min/max length
const usernameSchema = z
  .string()
  .min(3, 'Username must be at least 3 characters')
  .max(20, 'Username must be less than 20 characters')

// Pattern matching
const phoneSchema = z
  .string()
  .regex(/^\+?[0-9]{10,14}$/, 'Please enter a valid phone number')

// Date validation
const futureDateSchema = z
  .date()
  .min(new Date(), 'Date must be in the future')

// File validation
const imageSchema = z
  .instanceof(File)
  .refine((file) => file.size <= 5 * 1024 * 1024, 'File must be less than 5MB')
  .refine(
    (file) => ['image/jpeg', 'image/png', 'image/webp'].includes(file.type),
    'File must be JPEG, PNG, or WebP'
  )
```

### Form Validation Hook

```tsx
import { useState } from 'react'

function useFormValidation<T extends Record<string, any>>(
  initialValues: T,
  validationSchema: z.ZodType<T>
) {
  const [values, setValues] = useState(initialValues)
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({})
  const [touched, setTouched] = useState<Partial<Record<keyof T, boolean>>>({})

  const validate = () => {
    try {
      validationSchema.parse(values)
      setErrors({})
      return true
    } catch (err) {
      if (err instanceof z.ZodError) {
        const newErrors: Partial<Record<keyof T, string>> = {}
        err.errors.forEach((e) => {
          const field = e.path[0] as keyof T
          newErrors[field] = e.message
        })
        setErrors(newErrors)
      }
      return false
    }
  }

  const setValue = (field: keyof T, value: any) => {
    setValues((prev) => ({ ...prev, [field]: value }))
    // Clear error on change
    if (errors[field]) {
      setErrors((prev) => ({ ...prev, [field]: undefined }))
    }
  }

  const setFieldTouched = (field: keyof T) => {
    setTouched((prev) => ({ ...prev, [field]: true }))
  }

  return {
    values,
    errors,
    touched,
    setValue,
    setFieldTouched,
    validate,
  }
}
```

### Inline Validation Example

```tsx
function ContactForm() {
  const { values, errors, setValue, validate } = useFormValidation(
    { email: '', message: '' },
    z.object({
      email: z.string().email('Please enter a valid email'),
      message: z.string().min(10, 'Message must be at least 10 characters'),
    })
  )

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    if (validate()) {
      // Submit form
    }
  }

  return (
    <form onSubmit={handleSubmit} className="space-y-4">
      <Input
        label="Email"
        type="email"
        value={values.email}
        onChange={(e) => setValue('email', e.target.value)}
        error={errors.email}
      />
      <Textarea
        label="Message"
        value={values.message}
        onChange={(e) => setValue('message', e.target.value)}
        error={errors.message}
      />
      <Button type="submit" variant="solid">
        Send Message
      </Button>
    </form>
  )
}
```

---

## Do's and Don'ts

### Do's

- **Do** always include labels for inputs
- **Do** provide clear error messages that explain how to fix the issue
- **Do** use appropriate input types (`email`, `tel`, `url`, etc.)
- **Do** show validation feedback inline, near the input
- **Do** support keyboard navigation throughout forms
- **Do** group related inputs with fieldsets
- **Do** use consistent spacing between form fields (16-24px)
- **Do** indicate required fields clearly
- **Do** provide placeholder text as hints, not labels
- **Do** disable submit buttons during form submission

### Don'ts

- **Don't** use placeholder text as the only label
- **Don't** show error states before user interaction
- **Don't** rely solely on color to indicate errors
- **Don't** use generic error messages like "Invalid input"
- **Don't** clear the entire form on validation errors
- **Don't** auto-focus inputs that cause page scrolling
- **Don't** use all-caps for labels or error messages
- **Don't** hide required field indicators
- **Don't** disable browser autocomplete without good reason
- **Don't** use custom date pickers on mobile (use native)

---

## Form Layout Patterns

### Single Column (Default)

```tsx
<form className="space-y-6 max-w-md">
  <Input label="Full Name" />
  <Input label="Email" type="email" />
  <Input label="Phone" type="tel" />
  <Button type="submit">Submit</Button>
</form>
```

### Two Column

```tsx
<form className="space-y-6">
  <div className="grid grid-cols-2 gap-4">
    <Input label="First Name" />
    <Input label="Last Name" />
  </div>
  <Input label="Email" type="email" />
  <div className="grid grid-cols-2 gap-4">
    <Select label="Country" options={countries} />
    <Input label="Postal Code" />
  </div>
  <Button type="submit">Submit</Button>
</form>
```

### Sectioned Form

```tsx
<form className="space-y-8">
  <section>
    <h2 className="font-heading text-h3 mb-4">Personal Information</h2>
    <div className="space-y-4">
      <Input label="Full Name" />
      <Input label="Email" type="email" />
    </div>
  </section>

  <hr className="border-gray-200" />

  <section>
    <h2 className="font-heading text-h3 mb-4">Preferences</h2>
    <div className="space-y-4">
      <Toggle label="Email notifications" />
      <Toggle label="SMS notifications" />
    </div>
  </section>

  <div className="flex gap-3">
    <Button variant="outline">Cancel</Button>
    <Button variant="solid">Save Changes</Button>
  </div>
</form>
```

---

## Related Documentation

- [Buttons](./buttons.md) - Form submission buttons
- [Colors](./colors.md) - Input border and focus colors
- [Typography](./typography.md) - Label and helper text styles
- [Patterns](./patterns.md) - Form patterns and validation
