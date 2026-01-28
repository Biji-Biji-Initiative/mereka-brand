# Cards

> Cards are the primary content containers in Mereka. The design system includes **27 card variants** organized by type, state, and context.

**Figma Sections**: Collection Cards (11467:12960), Cards (12592:4206)

---

## Card Types Overview

| Category | Variants | Primary Usage |
|----------|----------|---------------|
| **Experience Cards** | 6 | Browse workshops, events, learning sessions |
| **Hub Cards** | 2 | Display organizations/hubs |
| **Expert Cards** | 4 | Show bookable experts and profiles |
| **Review Cards** | 2 | User reviews with ratings |
| **Collection Cards** | 3 | Curated lists and get started flows |
| **Single Page Cards** | 2 | Detailed expert/hub views |
| **Operating Hours Cards** | 3 | Business hours display |
| **Card Image Sizes** | 4 | xl, l, m, s variants |

---

## Card Image Sizes

All cards use standardized image sizing. Choose based on context and screen real estate.

| Size | Dimensions | Aspect Ratio | Usage |
|------|------------|--------------|-------|
| **xl** | 360 x 240px | 3:2 | Hero cards, featured experiences |
| **l** | 280 x 186px | 3:2 | Standard grid cards |
| **m** | 200 x 133px | 3:2 | Compact grids, sidebars |
| **s** | 120 x 80px | 3:2 | List views, mini cards |

### Image Size Implementation

```tsx
import { cva, type VariantProps } from 'class-variance-authority'

const cardImageVariants = cva(
  'relative overflow-hidden rounded-t-xl',
  {
    variants: {
      size: {
        xl: 'w-[360px] h-[240px]',
        l: 'w-[280px] h-[186px]',
        m: 'w-[200px] h-[133px]',
        s: 'w-[120px] h-[80px]',
        full: 'w-full aspect-[3/2]',
      },
    },
    defaultVariants: {
      size: 'full',
    },
  }
)

interface CardImageProps extends VariantProps<typeof cardImageVariants> {
  src: string
  alt: string
  className?: string
}

export function CardImage({ src, alt, size, className }: CardImageProps) {
  return (
    <div className={cardImageVariants({ size, className })}>
      <img
        src={src}
        alt={alt}
        className="w-full h-full object-cover"
      />
    </div>
  )
}
```

---

## 1. Experience Cards

Experience Cards display workshops, events, and learning sessions. They are the most common card type in the Mereka platform.

### Types

| Type | Description | Usage Context |
|------|-------------|---------------|
| **Collection (Grid)** | Standard grid layout | Experience browse pages |
| **Spaces** | Location-focused variant | Map view, location pages |
| **Experience (Collection)** | Rich metadata display | Curated collections |
| **Experience (Simple)** | Minimal info display | Compact lists, suggestions |

### Categories

| Category | Visual Treatment | Context |
|----------|-----------------|---------|
| **Default** | Standard styling | General browse |
| **Map** | Compact with location emphasis | Map overlays |
| **Hub Profile** | Branded with hub context | Hub profile pages |

### States

| State | Visual Treatment |
|-------|------------------|
| **Default** | Flat shadow, neutral |
| **Hover** | Elevated shadow, image scale, cursor pointer |

---

### Experience Card - Collection (Grid)

The primary card for browsing experiences in grid layouts.

**When to use**: Experience listing pages, search results, homepage grids

```tsx
interface ExperienceCardProps {
  // Required
  id: string
  title: string
  coverImage: string
  price: number
  currency: string
  host: {
    name: string
    avatar: string
  }

  // Optional
  description?: string
  date?: Date
  location?: {
    type: 'physical' | 'virtual' | 'hybrid'
    address?: string
    city?: string
  }
  rating?: number
  reviewCount?: number
  category?: string
  isSaved?: boolean
  isNew?: boolean
  spotsLeft?: number
}

function ExperienceCard({
  id,
  title,
  coverImage,
  price,
  currency,
  host,
  description,
  date,
  location,
  rating,
  reviewCount,
  category,
  isSaved = false,
  isNew = false,
  spotsLeft,
}: ExperienceCardProps) {
  return (
    <article className="bg-white rounded-xl shadow-md overflow-hidden group cursor-pointer hover:shadow-lg transition-shadow">
      {/* Image Container */}
      <div className="aspect-[3/2] relative overflow-hidden">
        <img
          src={coverImage}
          alt={title}
          className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
        />

        {/* Top badges */}
        <div className="absolute top-3 left-3 right-3 flex justify-between items-start">
          {/* Location badge */}
          {location && (
            <span className="bg-white/90 backdrop-blur-sm rounded-full px-3 py-1 text-caption font-medium">
              {location.type === 'virtual' ? 'Online' : location.city || location.address}
            </span>
          )}

          {/* Save button */}
          <button
            className="w-8 h-8 rounded-full bg-white/90 backdrop-blur-sm flex items-center justify-center hover:bg-white transition-colors"
            aria-label={isSaved ? 'Unsave' : 'Save'}
          >
            <HeartIcon className={`w-4 h-4 ${isSaved ? 'fill-mereka-magenta text-mereka-magenta' : 'text-gray-600'}`} />
          </button>
        </div>

        {/* New badge */}
        {isNew && (
          <span className="absolute bottom-3 left-3 bg-mereka-teal text-white rounded-full px-3 py-1 text-caption font-medium">
            New
          </span>
        )}

        {/* Spots left warning */}
        {spotsLeft !== undefined && spotsLeft <= 3 && spotsLeft > 0 && (
          <span className="absolute bottom-3 right-3 bg-mereka-orange text-white rounded-full px-3 py-1 text-caption font-medium">
            {spotsLeft} {spotsLeft === 1 ? 'spot' : 'spots'} left
          </span>
        )}
      </div>

      {/* Content */}
      <div className="p-4">
        {/* Category */}
        {category && (
          <span className="text-overline text-mereka-teal uppercase tracking-wide">
            {category}
          </span>
        )}

        {/* Host */}
        <div className="flex items-center gap-2 mt-2">
          <img
            src={host.avatar}
            alt={host.name}
            className="w-6 h-6 rounded-full object-cover"
          />
          <span className="text-caption text-gray-600">{host.name}</span>
        </div>

        {/* Title */}
        <h3 className="font-heading text-h4 text-gray-900 mt-2 line-clamp-2">
          {title}
        </h3>

        {/* Description (optional) */}
        {description && (
          <p className="text-body-sm text-gray-600 mt-1 line-clamp-2">
            {description}
          </p>
        )}

        {/* Rating */}
        {rating !== undefined && (
          <div className="flex items-center gap-1 mt-2">
            <StarIcon className="w-4 h-4 text-mereka-gold fill-mereka-gold" />
            <span className="text-body-sm font-medium">{rating.toFixed(1)}</span>
            {reviewCount !== undefined && (
              <span className="text-caption text-gray-400">({reviewCount})</span>
            )}
          </div>
        )}

        {/* Footer */}
        <div className="flex items-center justify-between mt-4 pt-4 border-t border-gray-100">
          <div>
            <span className="text-caption text-gray-400">From</span>
            <p className="font-heading text-h4 text-gray-900">
              {currency} {price.toLocaleString()}
            </p>
          </div>
          {date && (
            <span className="text-body-sm text-gray-600">
              {formatDate(date)}
            </span>
          )}
        </div>
      </div>
    </article>
  )
}
```

### Usage Example

```tsx
// Browse page grid
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
  <ExperienceCard
    id="exp-001"
    title="Introduction to Pottery: Wheel Throwing for Beginners"
    coverImage="/images/pottery-workshop.jpg"
    price={150}
    currency="RM"
    host={{
      name: "Clay Studio KL",
      avatar: "/images/clay-studio-avatar.jpg"
    }}
    date={new Date('2024-01-20')}
    location={{
      type: 'physical',
      city: 'Kuala Lumpur'
    }}
    category="Arts & Crafts"
    rating={4.8}
    reviewCount={42}
    isNew={true}
  />
</div>
```

---

### Experience Card - Spaces Variant

Location-focused cards for map views and location browsing.

**When to use**: Map overlays, location-based search, space discovery

```tsx
interface SpacesCardProps {
  id: string
  title: string
  coverImage: string
  address: string
  distance?: string
  experienceCount: number
  priceRange?: {
    min: number
    max: number
    currency: string
  }
}

function SpacesCard({
  id,
  title,
  coverImage,
  address,
  distance,
  experienceCount,
  priceRange,
}: SpacesCardProps) {
  return (
    <article className="bg-white rounded-xl shadow-md overflow-hidden flex group cursor-pointer hover:shadow-lg transition-shadow w-[320px]">
      {/* Image */}
      <div className="w-24 h-24 flex-shrink-0 overflow-hidden">
        <img
          src={coverImage}
          alt={title}
          className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
        />
      </div>

      {/* Content */}
      <div className="flex-1 p-3 min-w-0">
        <h3 className="font-heading text-body font-semibold text-gray-900 truncate">
          {title}
        </h3>

        <div className="flex items-center gap-1 mt-1 text-caption text-gray-500">
          <MapPinIcon className="w-3 h-3 flex-shrink-0" />
          <span className="truncate">{address}</span>
        </div>

        {distance && (
          <span className="text-caption text-gray-400 mt-0.5 block">
            {distance} away
          </span>
        )}

        <div className="flex items-center justify-between mt-2">
          <span className="text-caption text-gray-600">
            {experienceCount} {experienceCount === 1 ? 'experience' : 'experiences'}
          </span>

          {priceRange && (
            <span className="text-caption font-medium text-gray-900">
              {priceRange.currency} {priceRange.min} - {priceRange.max}
            </span>
          )}
        </div>
      </div>
    </article>
  )
}
```

---

### Experience Card - Simple Variant

Minimal cards for compact displays and suggestions.

**When to use**: Related experiences, sidebar suggestions, compact lists

```tsx
interface ExperienceSimpleCardProps {
  id: string
  title: string
  coverImage: string
  price: number
  currency: string
  hostName: string
}

function ExperienceSimpleCard({
  id,
  title,
  coverImage,
  price,
  currency,
  hostName,
}: ExperienceSimpleCardProps) {
  return (
    <article className="flex gap-3 p-2 rounded-lg hover:bg-gray-50 cursor-pointer transition-colors">
      <img
        src={coverImage}
        alt={title}
        className="w-16 h-16 rounded-lg object-cover flex-shrink-0"
      />
      <div className="flex-1 min-w-0 py-0.5">
        <h4 className="text-body-sm font-medium text-gray-900 line-clamp-2">
          {title}
        </h4>
        <p className="text-caption text-gray-500 mt-0.5">{hostName}</p>
        <p className="text-body-sm font-semibold text-gray-900 mt-1">
          {currency} {price.toLocaleString()}
        </p>
      </div>
    </article>
  )
}
```

---

### Experience Card - Hub Profile Variant

Cards displayed within a hub's profile page, branded to the hub context.

**When to use**: Hub profile pages, hub experience listings

```tsx
interface ExperienceHubProfileCardProps {
  id: string
  title: string
  coverImage: string
  price: number
  currency: string
  date?: Date
  spotsLeft?: number
  duration?: string
  format: 'physical' | 'virtual' | 'hybrid'
}

function ExperienceHubProfileCard({
  id,
  title,
  coverImage,
  price,
  currency,
  date,
  spotsLeft,
  duration,
  format,
}: ExperienceHubProfileCardProps) {
  return (
    <article className="bg-white rounded-xl border border-gray-200 overflow-hidden group cursor-pointer hover:border-gray-300 hover:shadow-md transition-all">
      {/* Image */}
      <div className="aspect-[3/2] relative overflow-hidden">
        <img
          src={coverImage}
          alt={title}
          className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
        />

        {/* Format badge */}
        <span className="absolute top-3 left-3 bg-white/90 backdrop-blur-sm rounded-full px-3 py-1 text-caption font-medium capitalize">
          {format}
        </span>
      </div>

      {/* Content */}
      <div className="p-4">
        <h3 className="font-heading text-h5 text-gray-900 line-clamp-2">
          {title}
        </h3>

        {/* Meta info */}
        <div className="flex flex-wrap gap-x-3 gap-y-1 mt-2 text-caption text-gray-500">
          {date && (
            <span className="flex items-center gap-1">
              <CalendarIcon className="w-3.5 h-3.5" />
              {formatDate(date)}
            </span>
          )}
          {duration && (
            <span className="flex items-center gap-1">
              <ClockIcon className="w-3.5 h-3.5" />
              {duration}
            </span>
          )}
        </div>

        {/* Footer */}
        <div className="flex items-center justify-between mt-4 pt-3 border-t border-gray-100">
          <p className="font-heading text-h5 text-gray-900">
            {currency} {price.toLocaleString()}
          </p>

          {spotsLeft !== undefined && spotsLeft <= 5 && (
            <span className={`text-caption font-medium ${spotsLeft === 0 ? 'text-gray-400' : 'text-mereka-orange'}`}>
              {spotsLeft === 0 ? 'Sold out' : `${spotsLeft} left`}
            </span>
          )}
        </div>
      </div>
    </article>
  )
}
```

---

## 2. Hub Cards

Hub Cards display organization/hub information.

### States

| State | Visual Treatment |
|-------|------------------|
| **Default** | Standard shadow, neutral background |
| **Hover** | Elevated shadow, subtle background shift |

---

### Hub Card - Standard

**When to use**: Hub browse pages, search results, hub directories

```tsx
interface HubCardProps {
  // Required
  id: string
  name: string
  logo: string

  // Optional
  description?: string
  location?: string
  experienceCount?: number
  rating?: number
  reviewCount?: number
  categories?: string[]
  isVerified?: boolean
}

function HubCard({
  id,
  name,
  logo,
  description,
  location,
  experienceCount,
  rating,
  reviewCount,
  categories,
  isVerified = false,
}: HubCardProps) {
  return (
    <article className="bg-white rounded-xl shadow-md p-6 group cursor-pointer hover:shadow-lg transition-shadow">
      <div className="flex gap-4">
        {/* Logo */}
        <div className="relative">
          <img
            src={logo}
            alt={name}
            className="w-16 h-16 rounded-xl object-cover flex-shrink-0"
          />
          {isVerified && (
            <div className="absolute -bottom-1 -right-1 w-5 h-5 bg-mereka-teal rounded-full flex items-center justify-center">
              <CheckIcon className="w-3 h-3 text-white" />
            </div>
          )}
        </div>

        {/* Content */}
        <div className="flex-1 min-w-0">
          <div className="flex items-center gap-2">
            <h3 className="font-heading text-h4 text-gray-900 truncate">
              {name}
            </h3>
          </div>

          {description && (
            <p className="text-body-sm text-gray-600 mt-1 line-clamp-2">
              {description}
            </p>
          )}

          {/* Meta */}
          <div className="flex flex-wrap items-center gap-x-4 gap-y-1 mt-3 text-caption text-gray-500">
            {location && (
              <span className="flex items-center gap-1">
                <MapPinIcon className="w-3.5 h-3.5" />
                {location}
              </span>
            )}
            {experienceCount !== undefined && (
              <span>{experienceCount} experiences</span>
            )}
            {rating !== undefined && (
              <span className="flex items-center gap-1">
                <StarIcon className="w-3.5 h-3.5 text-mereka-gold fill-mereka-gold" />
                {rating.toFixed(1)}
                {reviewCount !== undefined && (
                  <span className="text-gray-400">({reviewCount})</span>
                )}
              </span>
            )}
          </div>

          {/* Categories */}
          {categories && categories.length > 0 && (
            <div className="flex flex-wrap gap-2 mt-3">
              {categories.slice(0, 3).map((category) => (
                <span
                  key={category}
                  className="bg-gray-100 text-gray-600 rounded-full px-2.5 py-0.5 text-caption"
                >
                  {category}
                </span>
              ))}
              {categories.length > 3 && (
                <span className="text-caption text-gray-400">
                  +{categories.length - 3} more
                </span>
              )}
            </div>
          )}
        </div>
      </div>
    </article>
  )
}
```

### Usage Example

```tsx
<HubCard
  id="hub-001"
  name="The Clay Studio"
  logo="/images/clay-studio-logo.jpg"
  description="A creative space dedicated to the art of ceramics and pottery, offering workshops for all skill levels."
  location="Bangsar, KL"
  experienceCount={12}
  rating={4.9}
  reviewCount={156}
  categories={['Arts & Crafts', 'Ceramics', 'Workshops']}
  isVerified={true}
/>
```

---

### Hub Card - Compact

For tighter layouts like sidebars and lists.

**When to use**: Sidebar recommendations, compact lists, related hubs

```tsx
interface HubCompactCardProps {
  id: string
  name: string
  logo: string
  location?: string
  experienceCount?: number
}

function HubCompactCard({
  id,
  name,
  logo,
  location,
  experienceCount,
}: HubCompactCardProps) {
  return (
    <article className="flex items-center gap-3 p-3 rounded-lg hover:bg-gray-50 cursor-pointer transition-colors">
      <img
        src={logo}
        alt={name}
        className="w-12 h-12 rounded-lg object-cover flex-shrink-0"
      />
      <div className="flex-1 min-w-0">
        <h4 className="text-body font-medium text-gray-900 truncate">{name}</h4>
        <div className="flex items-center gap-2 text-caption text-gray-500">
          {location && <span>{location}</span>}
          {location && experienceCount !== undefined && <span>·</span>}
          {experienceCount !== undefined && (
            <span>{experienceCount} experiences</span>
          )}
        </div>
      </div>
      <ChevronRightIcon className="w-5 h-5 text-gray-400 flex-shrink-0" />
    </article>
  )
}
```

---

## 3. Expert Cards

Expert Cards showcase individuals who offer bookable sessions or expertise.

### Types

| Type | Description | Context |
|------|-------------|---------|
| **Bookable Expert Card** | Full card with booking CTA | Expert browse, search |
| **Expert Profile Card** | Detailed with expertise | Expert detail pages |
| **Expertise Card** | Skill-focused variant | Expertise listings |
| **Expertise Home Card** | Homepage featured | Homepage, featured sections |

---

### Bookable Expert Card

**When to use**: Expert browse pages, search results, expert directories

```tsx
interface BookableExpertCardProps {
  // Required
  id: string
  name: string
  avatar: string
  title: string
  hourlyRate: number
  currency: string

  // Optional
  expertise?: string[]
  bio?: string
  rating?: number
  reviewCount?: number
  sessionCount?: number
  availability?: 'available' | 'busy' | 'unavailable'
  isTopExpert?: boolean
  languages?: string[]
}

function BookableExpertCard({
  id,
  name,
  avatar,
  title,
  hourlyRate,
  currency,
  expertise,
  bio,
  rating,
  reviewCount,
  sessionCount,
  availability = 'available',
  isTopExpert = false,
  languages,
}: BookableExpertCardProps) {
  const availabilityStyles = {
    available: 'bg-green-100 text-green-700',
    busy: 'bg-yellow-100 text-yellow-700',
    unavailable: 'bg-gray-100 text-gray-500',
  }

  const availabilityLabels = {
    available: 'Available',
    busy: 'Limited availability',
    unavailable: 'Unavailable',
  }

  return (
    <article className="bg-white rounded-xl shadow-md overflow-hidden group cursor-pointer hover:shadow-lg transition-shadow">
      {/* Header with avatar */}
      <div className="relative p-6 pb-4">
        {/* Top Expert badge */}
        {isTopExpert && (
          <span className="absolute top-3 right-3 bg-mereka-gold text-gray-900 rounded-full px-3 py-1 text-caption font-medium flex items-center gap-1">
            <StarIcon className="w-3 h-3" />
            Top Expert
          </span>
        )}

        <div className="flex items-start gap-4">
          {/* Avatar */}
          <div className="relative">
            <img
              src={avatar}
              alt={name}
              className="w-20 h-20 rounded-full object-cover"
            />
            {/* Availability indicator */}
            <span className={`absolute bottom-0 right-0 w-5 h-5 rounded-full border-2 border-white ${
              availability === 'available' ? 'bg-green-500' :
              availability === 'busy' ? 'bg-yellow-500' : 'bg-gray-400'
            }`} />
          </div>

          {/* Info */}
          <div className="flex-1 min-w-0">
            <h3 className="font-heading text-h4 text-gray-900">{name}</h3>
            <p className="text-body-sm text-gray-600 mt-0.5">{title}</p>

            {/* Rating */}
            {rating !== undefined && (
              <div className="flex items-center gap-1 mt-2">
                <StarIcon className="w-4 h-4 text-mereka-gold fill-mereka-gold" />
                <span className="text-body-sm font-medium">{rating.toFixed(1)}</span>
                {reviewCount !== undefined && (
                  <span className="text-caption text-gray-400">({reviewCount} reviews)</span>
                )}
              </div>
            )}
          </div>
        </div>
      </div>

      {/* Content */}
      <div className="px-6 pb-6">
        {/* Bio */}
        {bio && (
          <p className="text-body-sm text-gray-600 line-clamp-2">{bio}</p>
        )}

        {/* Expertise tags */}
        {expertise && expertise.length > 0 && (
          <div className="flex flex-wrap gap-2 mt-3">
            {expertise.slice(0, 3).map((skill) => (
              <span
                key={skill}
                className="bg-mereka-sky/30 text-mereka-blue rounded-full px-2.5 py-0.5 text-caption"
              >
                {skill}
              </span>
            ))}
            {expertise.length > 3 && (
              <span className="text-caption text-gray-400">+{expertise.length - 3}</span>
            )}
          </div>
        )}

        {/* Languages */}
        {languages && languages.length > 0 && (
          <p className="text-caption text-gray-500 mt-2">
            Speaks: {languages.join(', ')}
          </p>
        )}

        {/* Footer */}
        <div className="flex items-center justify-between mt-4 pt-4 border-t border-gray-100">
          <div>
            <span className="text-caption text-gray-400">From</span>
            <p className="font-heading text-h4 text-gray-900">
              {currency} {hourlyRate}/hr
            </p>
          </div>

          <span className={`rounded-full px-3 py-1 text-caption font-medium ${availabilityStyles[availability]}`}>
            {availabilityLabels[availability]}
          </span>
        </div>

        {/* Book button */}
        {availability !== 'unavailable' && (
          <button className="w-full mt-4 bg-gray-900 text-white rounded-full py-2.5 font-medium hover:bg-gray-800 transition-colors">
            Book Session
          </button>
        )}
      </div>
    </article>
  )
}
```

### Usage Example

```tsx
<BookableExpertCard
  id="expert-001"
  name="Dr. Sarah Chen"
  avatar="/images/sarah-chen.jpg"
  title="Leadership Coach & Executive Mentor"
  hourlyRate={350}
  currency="RM"
  expertise={['Leadership', 'Executive Coaching', 'Career Development', 'Team Building']}
  bio="15+ years helping executives and teams unlock their potential through evidence-based coaching methodologies."
  rating={4.9}
  reviewCount={89}
  sessionCount={234}
  availability="available"
  isTopExpert={true}
  languages={['English', 'Mandarin', 'Bahasa Malaysia']}
/>
```

---

### Expert Profile Card

Detailed card used on expert detail pages and featured sections.

**When to use**: Expert profile pages, featured expert sections

```tsx
interface ExpertProfileCardProps {
  id: string
  name: string
  avatar: string
  title: string
  bio: string
  expertise: string[]
  hourlyRate: number
  currency: string
  rating: number
  reviewCount: number
  totalSessions: number
  responseTime?: string
  hub?: {
    name: string
    logo: string
  }
}

function ExpertProfileCard({
  id,
  name,
  avatar,
  title,
  bio,
  expertise,
  hourlyRate,
  currency,
  rating,
  reviewCount,
  totalSessions,
  responseTime,
  hub,
}: ExpertProfileCardProps) {
  return (
    <article className="bg-white rounded-xl shadow-lg p-6">
      {/* Header */}
      <div className="flex items-start gap-4">
        <img
          src={avatar}
          alt={name}
          className="w-24 h-24 rounded-full object-cover"
        />
        <div className="flex-1">
          <h2 className="font-heading text-h3 text-gray-900">{name}</h2>
          <p className="text-body text-gray-600 mt-1">{title}</p>

          {hub && (
            <div className="flex items-center gap-2 mt-2">
              <img src={hub.logo} alt={hub.name} className="w-5 h-5 rounded" />
              <span className="text-body-sm text-gray-500">{hub.name}</span>
            </div>
          )}
        </div>
      </div>

      {/* Stats */}
      <div className="grid grid-cols-3 gap-4 mt-6 py-4 border-y border-gray-100">
        <div className="text-center">
          <div className="flex items-center justify-center gap-1">
            <StarIcon className="w-5 h-5 text-mereka-gold fill-mereka-gold" />
            <span className="font-heading text-h4">{rating.toFixed(1)}</span>
          </div>
          <p className="text-caption text-gray-500 mt-1">{reviewCount} reviews</p>
        </div>
        <div className="text-center border-x border-gray-100">
          <p className="font-heading text-h4">{totalSessions}</p>
          <p className="text-caption text-gray-500 mt-1">Sessions</p>
        </div>
        <div className="text-center">
          <p className="font-heading text-h4">{responseTime || '< 1hr'}</p>
          <p className="text-caption text-gray-500 mt-1">Response time</p>
        </div>
      </div>

      {/* Bio */}
      <p className="text-body text-gray-600 mt-4">{bio}</p>

      {/* Expertise */}
      <div className="mt-4">
        <h4 className="text-overline text-gray-500 uppercase tracking-wide mb-2">
          Expertise
        </h4>
        <div className="flex flex-wrap gap-2">
          {expertise.map((skill) => (
            <span
              key={skill}
              className="bg-gray-100 text-gray-700 rounded-full px-3 py-1 text-body-sm"
            >
              {skill}
            </span>
          ))}
        </div>
      </div>

      {/* Booking CTA */}
      <div className="mt-6 pt-4 border-t border-gray-100">
        <div className="flex items-center justify-between mb-4">
          <div>
            <span className="text-caption text-gray-400">Starting from</span>
            <p className="font-heading text-h3 text-gray-900">
              {currency} {hourlyRate}<span className="text-body text-gray-500">/hr</span>
            </p>
          </div>
        </div>
        <button className="w-full bg-mereka-teal text-white rounded-full py-3 font-medium hover:bg-mereka-teal/90 transition-colors">
          Book a Session
        </button>
      </div>
    </article>
  )
}
```

---

### Expertise Card

Skill-focused cards for browsing by expertise area.

**When to use**: Expertise category pages, skill-based navigation

```tsx
interface ExpertiseCardProps {
  id: string
  name: string
  icon: React.ReactNode
  expertCount: number
  description?: string
  color?: string
}

function ExpertiseCard({
  id,
  name,
  icon,
  expertCount,
  description,
  color = 'bg-gray-100',
}: ExpertiseCardProps) {
  return (
    <article className="bg-white rounded-xl border border-gray-200 p-5 cursor-pointer hover:border-gray-300 hover:shadow-md transition-all group">
      <div className={`w-12 h-12 rounded-xl ${color} flex items-center justify-center mb-4 group-hover:scale-110 transition-transform`}>
        {icon}
      </div>
      <h3 className="font-heading text-h5 text-gray-900">{name}</h3>
      {description && (
        <p className="text-body-sm text-gray-600 mt-1 line-clamp-2">{description}</p>
      )}
      <p className="text-caption text-gray-500 mt-2">
        {expertCount} {expertCount === 1 ? 'expert' : 'experts'}
      </p>
    </article>
  )
}
```

---

### Expertise Home Card

Featured expertise cards for the homepage.

**When to use**: Homepage featured sections, landing pages

```tsx
interface ExpertiseHomeCardProps {
  id: string
  name: string
  coverImage: string
  expertCount: number
  topExperts?: Array<{
    avatar: string
    name: string
  }>
}

function ExpertiseHomeCard({
  id,
  name,
  coverImage,
  expertCount,
  topExperts,
}: ExpertiseHomeCardProps) {
  return (
    <article className="relative rounded-xl overflow-hidden group cursor-pointer aspect-[4/3]">
      {/* Background image */}
      <img
        src={coverImage}
        alt={name}
        className="absolute inset-0 w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
      />

      {/* Gradient overlay */}
      <div className="absolute inset-0 bg-gradient-to-t from-black/80 via-black/20 to-transparent" />

      {/* Content */}
      <div className="absolute inset-x-0 bottom-0 p-5 text-white">
        <h3 className="font-heading text-h4">{name}</h3>
        <p className="text-body-sm text-white/80 mt-1">
          {expertCount} experts available
        </p>

        {/* Top experts avatars */}
        {topExperts && topExperts.length > 0 && (
          <div className="flex items-center mt-3">
            <div className="flex -space-x-2">
              {topExperts.slice(0, 4).map((expert, index) => (
                <img
                  key={index}
                  src={expert.avatar}
                  alt={expert.name}
                  className="w-8 h-8 rounded-full border-2 border-white object-cover"
                />
              ))}
            </div>
            {topExperts.length > 4 && (
              <span className="text-caption text-white/60 ml-2">
                +{topExperts.length - 4} more
              </span>
            )}
          </div>
        )}
      </div>
    </article>
  )
}
```

---

## 4. Review Cards

Review Cards display user feedback and ratings.

### States

| State | Context |
|-------|---------|
| **Default** | Standard review lists |
| **Hub Profile** | Reviews on hub pages with hub context |

---

### Review Card - Default

**When to use**: Review sections, experience detail pages, expert profiles

```tsx
interface ReviewCardProps {
  // Required
  id: string
  author: {
    name: string
    avatar?: string
  }
  rating: number
  content: string
  date: Date

  // Optional
  experience?: {
    title: string
    id: string
  }
  helpful?: number
  verified?: boolean
  response?: {
    author: string
    content: string
    date: Date
  }
}

function ReviewCard({
  id,
  author,
  rating,
  content,
  date,
  experience,
  helpful,
  verified = false,
  response,
}: ReviewCardProps) {
  return (
    <article className="bg-white rounded-xl border border-gray-200 p-5">
      {/* Header */}
      <div className="flex items-start justify-between">
        <div className="flex items-center gap-3">
          {/* Avatar */}
          {author.avatar ? (
            <img
              src={author.avatar}
              alt={author.name}
              className="w-10 h-10 rounded-full object-cover"
            />
          ) : (
            <div className="w-10 h-10 rounded-full bg-gray-200 flex items-center justify-center">
              <span className="text-body font-medium text-gray-600">
                {author.name.charAt(0).toUpperCase()}
              </span>
            </div>
          )}

          <div>
            <div className="flex items-center gap-2">
              <span className="font-medium text-gray-900">{author.name}</span>
              {verified && (
                <span className="bg-green-100 text-green-700 rounded-full px-2 py-0.5 text-caption flex items-center gap-1">
                  <CheckIcon className="w-3 h-3" />
                  Verified
                </span>
              )}
            </div>
            <span className="text-caption text-gray-500">
              {formatRelativeTime(date)}
            </span>
          </div>
        </div>

        {/* Rating */}
        <div className="flex items-center gap-0.5">
          {[1, 2, 3, 4, 5].map((star) => (
            <StarIcon
              key={star}
              className={`w-4 h-4 ${
                star <= rating
                  ? 'text-mereka-gold fill-mereka-gold'
                  : 'text-gray-300'
              }`}
            />
          ))}
        </div>
      </div>

      {/* Experience reference */}
      {experience && (
        <a
          href={`/experiences/${experience.id}`}
          className="text-body-sm text-mereka-blue hover:underline mt-2 block"
        >
          {experience.title}
        </a>
      )}

      {/* Review content */}
      <p className="text-body text-gray-700 mt-3">{content}</p>

      {/* Response from host/hub */}
      {response && (
        <div className="mt-4 pl-4 border-l-2 border-gray-200">
          <p className="text-caption text-gray-500">
            Response from {response.author} · {formatRelativeTime(response.date)}
          </p>
          <p className="text-body-sm text-gray-600 mt-1">{response.content}</p>
        </div>
      )}

      {/* Footer */}
      <div className="flex items-center gap-4 mt-4 pt-3 border-t border-gray-100">
        <button className="flex items-center gap-1.5 text-caption text-gray-500 hover:text-gray-700 transition-colors">
          <ThumbsUpIcon className="w-4 h-4" />
          Helpful{helpful !== undefined && helpful > 0 && ` (${helpful})`}
        </button>
        <button className="text-caption text-gray-500 hover:text-gray-700 transition-colors">
          Report
        </button>
      </div>
    </article>
  )
}
```

### Usage Example

```tsx
<ReviewCard
  id="review-001"
  author={{
    name: "Michelle Tan",
    avatar: "/images/michelle.jpg"
  }}
  rating={5}
  content="Absolutely loved this workshop! The instructor was patient and really took time to explain each technique. I left with a beautiful bowl that I made myself. Highly recommend for beginners."
  date={new Date('2024-01-15')}
  experience={{
    title: "Introduction to Pottery: Wheel Throwing",
    id: "exp-001"
  }}
  helpful={12}
  verified={true}
  response={{
    author: "Clay Studio KL",
    content: "Thank you so much for the kind words, Michelle! We're thrilled you enjoyed the workshop. Can't wait to see you at our next session!",
    date: new Date('2024-01-16')
  }}
/>
```

---

### Review Card - Hub Profile Variant

Reviews displayed on hub profile pages with hub branding context.

**When to use**: Hub profile review sections

```tsx
interface HubProfileReviewCardProps {
  id: string
  author: {
    name: string
    avatar?: string
  }
  rating: number
  content: string
  date: Date
  experience: {
    title: string
    coverImage: string
  }
}

function HubProfileReviewCard({
  id,
  author,
  rating,
  content,
  date,
  experience,
}: HubProfileReviewCardProps) {
  return (
    <article className="bg-gray-50 rounded-xl p-5">
      {/* Experience context */}
      <div className="flex items-center gap-3 pb-4 mb-4 border-b border-gray-200">
        <img
          src={experience.coverImage}
          alt={experience.title}
          className="w-12 h-12 rounded-lg object-cover"
        />
        <p className="text-body-sm font-medium text-gray-900 line-clamp-2">
          {experience.title}
        </p>
      </div>

      {/* Review */}
      <div className="flex items-center gap-0.5 mb-2">
        {[1, 2, 3, 4, 5].map((star) => (
          <StarIcon
            key={star}
            className={`w-4 h-4 ${
              star <= rating
                ? 'text-mereka-gold fill-mereka-gold'
                : 'text-gray-300'
            }`}
          />
        ))}
      </div>

      <p className="text-body text-gray-700">{content}</p>

      {/* Author */}
      <div className="flex items-center gap-2 mt-4">
        {author.avatar ? (
          <img
            src={author.avatar}
            alt={author.name}
            className="w-6 h-6 rounded-full object-cover"
          />
        ) : (
          <div className="w-6 h-6 rounded-full bg-gray-300 flex items-center justify-center">
            <span className="text-caption font-medium text-gray-600">
              {author.name.charAt(0)}
            </span>
          </div>
        )}
        <span className="text-caption text-gray-600">{author.name}</span>
        <span className="text-caption text-gray-400">·</span>
        <span className="text-caption text-gray-400">{formatRelativeTime(date)}</span>
      </div>
    </article>
  )
}
```

---

## 5. Collection Cards

Collection Cards display curated lists and collections of experiences.

### Types

| Type | Description | Context |
|------|-------------|---------|
| **Collection Card** | Standard collection display | Browse collections |
| **Collection Preview** | With experience thumbnails | Featured collections |
| **Get Started Card** | Onboarding prompts | New user flows |

---

### Collection Card

**When to use**: Collection browse pages, curated lists

```tsx
interface CollectionCardProps {
  id: string
  title: string
  description?: string
  coverImage: string
  experienceCount: number
  author?: {
    name: string
    avatar: string
  }
  isOfficial?: boolean
}

function CollectionCard({
  id,
  title,
  description,
  coverImage,
  experienceCount,
  author,
  isOfficial = false,
}: CollectionCardProps) {
  return (
    <article className="bg-white rounded-xl shadow-md overflow-hidden group cursor-pointer hover:shadow-lg transition-shadow">
      {/* Image */}
      <div className="aspect-[16/9] relative overflow-hidden">
        <img
          src={coverImage}
          alt={title}
          className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
        />

        {/* Overlay gradient */}
        <div className="absolute inset-0 bg-gradient-to-t from-black/60 to-transparent" />

        {/* Official badge */}
        {isOfficial && (
          <span className="absolute top-3 left-3 bg-mereka-teal text-white rounded-full px-3 py-1 text-caption font-medium flex items-center gap-1">
            <SparklesIcon className="w-3 h-3" />
            Official
          </span>
        )}

        {/* Title overlay */}
        <div className="absolute inset-x-0 bottom-0 p-4 text-white">
          <h3 className="font-heading text-h4">{title}</h3>
          <p className="text-body-sm text-white/80 mt-1">
            {experienceCount} {experienceCount === 1 ? 'experience' : 'experiences'}
          </p>
        </div>
      </div>

      {/* Footer */}
      {(description || author) && (
        <div className="p-4">
          {description && (
            <p className="text-body-sm text-gray-600 line-clamp-2">{description}</p>
          )}

          {author && (
            <div className="flex items-center gap-2 mt-3">
              <img
                src={author.avatar}
                alt={author.name}
                className="w-6 h-6 rounded-full object-cover"
              />
              <span className="text-caption text-gray-500">
                Curated by {author.name}
              </span>
            </div>
          )}
        </div>
      )}
    </article>
  )
}
```

### Usage Example

```tsx
<CollectionCard
  id="collection-001"
  title="Weekend Creative Escapes"
  description="Perfect hands-on workshops for your weekend. Unwind, create, and learn something new."
  coverImage="/images/creative-collection.jpg"
  experienceCount={8}
  isOfficial={true}
/>
```

---

### Collection Preview Card

Collection card showing preview thumbnails of included experiences.

**When to use**: Featured collections, homepage

```tsx
interface CollectionPreviewCardProps {
  id: string
  title: string
  experienceCount: number
  previews: Array<{
    id: string
    image: string
    title: string
  }>
}

function CollectionPreviewCard({
  id,
  title,
  experienceCount,
  previews,
}: CollectionPreviewCardProps) {
  return (
    <article className="bg-white rounded-xl border border-gray-200 p-4 cursor-pointer hover:border-gray-300 hover:shadow-md transition-all group">
      {/* Preview grid */}
      <div className="grid grid-cols-3 gap-2">
        {previews.slice(0, 3).map((preview, index) => (
          <div
            key={preview.id}
            className={`aspect-square rounded-lg overflow-hidden ${
              index === 0 ? 'col-span-2 row-span-2' : ''
            }`}
          >
            <img
              src={preview.image}
              alt={preview.title}
              className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
            />
          </div>
        ))}
      </div>

      {/* Info */}
      <div className="mt-3">
        <h3 className="font-heading text-h5 text-gray-900 group-hover:text-mereka-teal transition-colors">
          {title}
        </h3>
        <p className="text-caption text-gray-500 mt-1">
          {experienceCount} experiences
        </p>
      </div>
    </article>
  )
}
```

---

### Get Started Card

Onboarding prompt cards for new users.

**When to use**: New user dashboards, onboarding flows, empty states

```tsx
interface GetStartedCardProps {
  title: string
  description: string
  icon: React.ReactNode
  actionLabel: string
  onAction: () => void
  isCompleted?: boolean
  order?: number
}

function GetStartedCard({
  title,
  description,
  icon,
  actionLabel,
  onAction,
  isCompleted = false,
  order,
}: GetStartedCardProps) {
  return (
    <article className={`bg-white rounded-xl border p-5 ${
      isCompleted ? 'border-green-200 bg-green-50' : 'border-gray-200'
    }`}>
      <div className="flex gap-4">
        {/* Icon/Step */}
        <div className={`w-12 h-12 rounded-full flex items-center justify-center flex-shrink-0 ${
          isCompleted
            ? 'bg-green-500 text-white'
            : 'bg-mereka-teal/10 text-mereka-teal'
        }`}>
          {isCompleted ? (
            <CheckIcon className="w-6 h-6" />
          ) : order !== undefined ? (
            <span className="font-heading text-h4">{order}</span>
          ) : (
            icon
          )}
        </div>

        {/* Content */}
        <div className="flex-1">
          <h3 className={`font-heading text-h5 ${
            isCompleted ? 'text-green-700' : 'text-gray-900'
          }`}>
            {title}
          </h3>
          <p className={`text-body-sm mt-1 ${
            isCompleted ? 'text-green-600' : 'text-gray-600'
          }`}>
            {description}
          </p>

          {!isCompleted && (
            <button
              onClick={onAction}
              className="mt-3 text-body-sm font-medium text-mereka-teal hover:text-mereka-teal/80 transition-colors inline-flex items-center gap-1"
            >
              {actionLabel}
              <ArrowRightIcon className="w-4 h-4" />
            </button>
          )}
        </div>
      </div>
    </article>
  )
}
```

### Usage Example

```tsx
<div className="space-y-3">
  <GetStartedCard
    title="Complete your profile"
    description="Add a photo and bio to help others get to know you."
    icon={<UserIcon className="w-6 h-6" />}
    actionLabel="Complete Profile"
    onAction={() => navigate('/settings/profile')}
    order={1}
    isCompleted={true}
  />
  <GetStartedCard
    title="Browse experiences"
    description="Discover workshops and learning sessions near you."
    icon={<SearchIcon className="w-6 h-6" />}
    actionLabel="Start Exploring"
    onAction={() => navigate('/experiences')}
    order={2}
    isCompleted={false}
  />
</div>
```

---

## 6. Single Page Cards

Large cards used in dedicated detail views.

---

### Single Page Card - Expert

Full-width expert card for expert detail pages.

**When to use**: Expert detail page headers

```tsx
interface SinglePageExpertCardProps {
  id: string
  name: string
  avatar: string
  coverImage?: string
  title: string
  bio: string
  expertise: string[]
  hourlyRate: number
  currency: string
  rating: number
  reviewCount: number
  totalSessions: number
  hub?: {
    name: string
    logo: string
  }
  socialLinks?: {
    linkedin?: string
    twitter?: string
    website?: string
  }
}

function SinglePageExpertCard({
  id,
  name,
  avatar,
  coverImage,
  title,
  bio,
  expertise,
  hourlyRate,
  currency,
  rating,
  reviewCount,
  totalSessions,
  hub,
  socialLinks,
}: SinglePageExpertCardProps) {
  return (
    <article className="bg-white rounded-2xl shadow-lg overflow-hidden">
      {/* Cover image */}
      {coverImage && (
        <div className="h-48 relative">
          <img
            src={coverImage}
            alt=""
            className="w-full h-full object-cover"
          />
          <div className="absolute inset-0 bg-gradient-to-t from-black/40 to-transparent" />
        </div>
      )}

      <div className="p-8">
        {/* Profile section */}
        <div className="flex flex-col md:flex-row gap-6">
          {/* Avatar */}
          <img
            src={avatar}
            alt={name}
            className={`w-32 h-32 rounded-full object-cover border-4 border-white shadow-lg ${
              coverImage ? '-mt-20' : ''
            }`}
          />

          {/* Info */}
          <div className="flex-1">
            <div className="flex flex-col md:flex-row md:items-start md:justify-between gap-4">
              <div>
                <h1 className="font-heading text-h2 text-gray-900">{name}</h1>
                <p className="text-body-lg text-gray-600 mt-1">{title}</p>

                {hub && (
                  <div className="flex items-center gap-2 mt-2">
                    <img src={hub.logo} alt={hub.name} className="w-6 h-6 rounded" />
                    <span className="text-body-sm text-gray-500">{hub.name}</span>
                  </div>
                )}
              </div>

              {/* Social links */}
              {socialLinks && (
                <div className="flex gap-2">
                  {socialLinks.linkedin && (
                    <a href={socialLinks.linkedin} className="w-10 h-10 rounded-full bg-gray-100 flex items-center justify-center hover:bg-gray-200 transition-colors">
                      <LinkedInIcon className="w-5 h-5 text-gray-600" />
                    </a>
                  )}
                  {socialLinks.twitter && (
                    <a href={socialLinks.twitter} className="w-10 h-10 rounded-full bg-gray-100 flex items-center justify-center hover:bg-gray-200 transition-colors">
                      <TwitterIcon className="w-5 h-5 text-gray-600" />
                    </a>
                  )}
                  {socialLinks.website && (
                    <a href={socialLinks.website} className="w-10 h-10 rounded-full bg-gray-100 flex items-center justify-center hover:bg-gray-200 transition-colors">
                      <GlobeIcon className="w-5 h-5 text-gray-600" />
                    </a>
                  )}
                </div>
              )}
            </div>

            {/* Stats */}
            <div className="flex flex-wrap gap-6 mt-6">
              <div>
                <div className="flex items-center gap-1">
                  <StarIcon className="w-5 h-5 text-mereka-gold fill-mereka-gold" />
                  <span className="font-heading text-h4">{rating.toFixed(1)}</span>
                </div>
                <p className="text-caption text-gray-500">{reviewCount} reviews</p>
              </div>
              <div>
                <p className="font-heading text-h4">{totalSessions}+</p>
                <p className="text-caption text-gray-500">Sessions</p>
              </div>
              <div>
                <p className="font-heading text-h4">
                  {currency} {hourlyRate}
                </p>
                <p className="text-caption text-gray-500">per hour</p>
              </div>
            </div>
          </div>
        </div>

        {/* Bio */}
        <div className="mt-8">
          <h2 className="font-heading text-h5 text-gray-900 mb-3">About</h2>
          <p className="text-body text-gray-600 whitespace-pre-line">{bio}</p>
        </div>

        {/* Expertise */}
        <div className="mt-6">
          <h2 className="font-heading text-h5 text-gray-900 mb-3">Expertise</h2>
          <div className="flex flex-wrap gap-2">
            {expertise.map((skill) => (
              <span
                key={skill}
                className="bg-mereka-sky/30 text-mereka-blue rounded-full px-4 py-1.5 text-body-sm"
              >
                {skill}
              </span>
            ))}
          </div>
        </div>

        {/* CTA */}
        <div className="mt-8 pt-6 border-t border-gray-200 flex flex-col sm:flex-row gap-4">
          <button className="flex-1 bg-mereka-teal text-white rounded-full py-3 font-medium hover:bg-mereka-teal/90 transition-colors">
            Book a Session
          </button>
          <button className="flex-1 border border-gray-300 text-gray-900 rounded-full py-3 font-medium hover:bg-gray-50 transition-colors">
            Send Message
          </button>
        </div>
      </div>
    </article>
  )
}
```

---

### Single Page Card - Hub

Full-width hub card for hub profile pages.

**When to use**: Hub profile page headers

```tsx
interface SinglePageHubCardProps {
  id: string
  name: string
  logo: string
  coverImage?: string
  description: string
  location: string
  experienceCount: number
  rating: number
  reviewCount: number
  memberSince: Date
  categories: string[]
  isVerified: boolean
  socialLinks?: {
    instagram?: string
    facebook?: string
    website?: string
  }
}

function SinglePageHubCard({
  id,
  name,
  logo,
  coverImage,
  description,
  location,
  experienceCount,
  rating,
  reviewCount,
  memberSince,
  categories,
  isVerified,
  socialLinks,
}: SinglePageHubCardProps) {
  return (
    <article className="bg-white rounded-2xl shadow-lg overflow-hidden">
      {/* Cover image */}
      {coverImage && (
        <div className="h-56 relative">
          <img
            src={coverImage}
            alt=""
            className="w-full h-full object-cover"
          />
        </div>
      )}

      <div className="p-8">
        {/* Header */}
        <div className="flex flex-col md:flex-row gap-6">
          {/* Logo */}
          <div className="relative">
            <img
              src={logo}
              alt={name}
              className={`w-24 h-24 rounded-2xl object-cover border-4 border-white shadow-lg ${
                coverImage ? '-mt-16' : ''
              }`}
            />
            {isVerified && (
              <div className="absolute -bottom-1 -right-1 w-7 h-7 bg-mereka-teal rounded-full flex items-center justify-center border-2 border-white">
                <CheckIcon className="w-4 h-4 text-white" />
              </div>
            )}
          </div>

          {/* Info */}
          <div className="flex-1">
            <div className="flex flex-col md:flex-row md:items-start md:justify-between gap-4">
              <div>
                <h1 className="font-heading text-h2 text-gray-900">{name}</h1>
                <div className="flex items-center gap-2 mt-1 text-body text-gray-500">
                  <MapPinIcon className="w-4 h-4" />
                  {location}
                </div>
              </div>

              {/* Actions */}
              <div className="flex gap-2">
                <button className="bg-mereka-teal text-white rounded-full px-6 py-2 font-medium hover:bg-mereka-teal/90 transition-colors">
                  Follow
                </button>
                <button className="border border-gray-300 rounded-full p-2 hover:bg-gray-50 transition-colors">
                  <ShareIcon className="w-5 h-5 text-gray-600" />
                </button>
              </div>
            </div>

            {/* Stats */}
            <div className="flex flex-wrap gap-6 mt-6">
              <div>
                <div className="flex items-center gap-1">
                  <StarIcon className="w-5 h-5 text-mereka-gold fill-mereka-gold" />
                  <span className="font-heading text-h4">{rating.toFixed(1)}</span>
                </div>
                <p className="text-caption text-gray-500">{reviewCount} reviews</p>
              </div>
              <div>
                <p className="font-heading text-h4">{experienceCount}</p>
                <p className="text-caption text-gray-500">Experiences</p>
              </div>
              <div>
                <p className="font-heading text-h4">
                  {new Date().getFullYear() - memberSince.getFullYear()}+ years
                </p>
                <p className="text-caption text-gray-500">On Mereka</p>
              </div>
            </div>
          </div>
        </div>

        {/* Description */}
        <p className="text-body text-gray-600 mt-6">{description}</p>

        {/* Categories */}
        <div className="flex flex-wrap gap-2 mt-4">
          {categories.map((category) => (
            <span
              key={category}
              className="bg-gray-100 text-gray-700 rounded-full px-3 py-1 text-body-sm"
            >
              {category}
            </span>
          ))}
        </div>

        {/* Social links */}
        {socialLinks && (
          <div className="flex gap-3 mt-6 pt-6 border-t border-gray-200">
            {socialLinks.website && (
              <a href={socialLinks.website} className="text-body-sm text-mereka-blue hover:underline flex items-center gap-1">
                <GlobeIcon className="w-4 h-4" />
                Website
              </a>
            )}
            {socialLinks.instagram && (
              <a href={socialLinks.instagram} className="text-body-sm text-mereka-blue hover:underline flex items-center gap-1">
                <InstagramIcon className="w-4 h-4" />
                Instagram
              </a>
            )}
            {socialLinks.facebook && (
              <a href={socialLinks.facebook} className="text-body-sm text-mereka-blue hover:underline flex items-center gap-1">
                <FacebookIcon className="w-4 h-4" />
                Facebook
              </a>
            )}
          </div>
        )}
      </div>
    </article>
  )
}
```

---

## 7. Operating Hours Cards

Display business/hub operating hours.

### States

| State | Visual Treatment | Usage |
|-------|------------------|-------|
| **Default** | Standard list | Full hours display |
| **Today** | Highlighted current day | Quick reference |
| **Closed** | Muted/grayed | Closed days |

---

### Operating Hours Card

**When to use**: Hub detail pages, contact sections

```tsx
interface DayHours {
  day: string
  open: string
  close: string
  isClosed?: boolean
}

interface OperatingHoursCardProps {
  hours: DayHours[]
  timezone?: string
  note?: string
}

function OperatingHoursCard({
  hours,
  timezone,
  note,
}: OperatingHoursCardProps) {
  const today = new Date().toLocaleDateString('en-US', { weekday: 'long' })

  return (
    <article className="bg-white rounded-xl border border-gray-200 p-5">
      <h3 className="font-heading text-h5 text-gray-900 mb-4 flex items-center gap-2">
        <ClockIcon className="w-5 h-5 text-gray-400" />
        Operating Hours
      </h3>

      <div className="space-y-2">
        {hours.map(({ day, open, close, isClosed }) => {
          const isToday = day === today

          return (
            <div
              key={day}
              className={`flex justify-between py-2 px-3 rounded-lg ${
                isToday ? 'bg-mereka-teal/10' : ''
              } ${isClosed ? 'text-gray-400' : ''}`}
            >
              <span className={`text-body-sm ${isToday ? 'font-medium text-mereka-teal' : 'text-gray-600'}`}>
                {day}
                {isToday && (
                  <span className="ml-2 text-caption bg-mereka-teal text-white rounded px-1.5 py-0.5">
                    Today
                  </span>
                )}
              </span>
              <span className={`text-body-sm ${isToday ? 'font-medium text-mereka-teal' : isClosed ? 'text-gray-400' : 'text-gray-900'}`}>
                {isClosed ? 'Closed' : `${open} - ${close}`}
              </span>
            </div>
          )
        })}
      </div>

      {timezone && (
        <p className="text-caption text-gray-400 mt-4">
          All times in {timezone}
        </p>
      )}

      {note && (
        <p className="text-caption text-gray-500 mt-2 p-3 bg-gray-50 rounded-lg">
          {note}
        </p>
      )}
    </article>
  )
}
```

### Usage Example

```tsx
<OperatingHoursCard
  hours={[
    { day: 'Monday', open: '10:00 AM', close: '6:00 PM' },
    { day: 'Tuesday', open: '10:00 AM', close: '6:00 PM' },
    { day: 'Wednesday', open: '10:00 AM', close: '8:00 PM' },
    { day: 'Thursday', open: '10:00 AM', close: '6:00 PM' },
    { day: 'Friday', open: '10:00 AM', close: '8:00 PM' },
    { day: 'Saturday', open: '9:00 AM', close: '5:00 PM' },
    { day: 'Sunday', isClosed: true, open: '', close: '' },
  ]}
  timezone="MYT (UTC+8)"
  note="Hours may vary on public holidays. Please call ahead to confirm."
/>
```

---

## Responsive Behavior

All cards should adapt gracefully across breakpoints.

### Grid Layouts

```tsx
// Experience cards
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4 md:gap-6">
  {experiences.map(exp => <ExperienceCard key={exp.id} {...exp} />)}
</div>

// Hub cards
<div className="grid grid-cols-1 lg:grid-cols-2 gap-4 md:gap-6">
  {hubs.map(hub => <HubCard key={hub.id} {...hub} />)}
</div>

// Expert cards
<div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4 md:gap-6">
  {experts.map(expert => <BookableExpertCard key={expert.id} {...expert} />)}
</div>
```

### Mobile Adaptations

| Card Type | Mobile Behavior |
|-----------|-----------------|
| Experience Card | Full width, reduced padding |
| Hub Card | Stacked layout (logo above content) |
| Expert Card | Compact avatar, truncated expertise |
| Collection Card | Horizontal scroll in carousels |
| Review Card | Full width, preserved content |

---

## Accessibility

All cards should follow accessibility best practices:

```tsx
// Semantic HTML
<article> // Use for card containers
<h3> // Card titles (adjust heading level based on context)

// Keyboard navigation
<article tabIndex={0} role="article">

// Screen reader context
<button aria-label="Save Introduction to Pottery experience">
  <HeartIcon />
</button>

// Image alt text
<img alt="Pottery workshop participants working on clay" />

// Focus states
className="focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2 focus:outline-none rounded-xl"
```

---

## Animation & Transitions

Standard card hover animations:

```tsx
// Image zoom
className="group-hover:scale-105 transition-transform duration-300"

// Shadow elevation
className="hover:shadow-lg transition-shadow"

// Border emphasis
className="hover:border-gray-300 transition-colors"

// Combined
className="group cursor-pointer hover:shadow-lg transition-all duration-200"
```

---

## Don'ts

- **Don't mix card types** in the same grid row
- **Don't omit hover states** - all interactive cards need feedback
- **Don't use arbitrary border radius** - stick to `rounded-xl` (16px) for cards
- **Don't truncate essential info** - price and date should always be visible
- **Don't forget loading states** - use skeleton cards while loading
- **Don't hardcode dimensions** - use aspect ratios and responsive widths
