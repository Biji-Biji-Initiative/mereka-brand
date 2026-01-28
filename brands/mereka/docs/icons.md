# Icons

> Mereka uses a consistent icon system across Experience Types, Space Categories, Social Logins, and general UI. We recommend **Lucide React** as the primary icon library.

**Figma Sections**: Styling > Icons (15541:5705, 15541:5744, 12114:9629)

---

## Icon Architecture

```
Icon Categories
├── Experience Type Icons (18)    ← What you'll learn
├── Space Category Icons (10)     ← Where it happens
├── Social Login Icons (4)        ← Authentication
└── UI Icons                      ← Navigation, actions, status
```

---

## Icon Library: Lucide React

We use [Lucide React](https://lucide.dev) as our primary icon library. It's lightweight, tree-shakeable, and provides consistent styling.

### Installation

```bash
npm install lucide-react
# or
bun add lucide-react
```

### Basic Usage

```tsx
import { Search, Heart, Calendar, MapPin } from 'lucide-react'

// Default size (24px)
<Search />

// Custom size
<Heart className="w-5 h-5" />

// With color
<Calendar className="w-4 h-4 text-mereka-teal" />

// Stroke width (default is 2)
<MapPin strokeWidth={1.5} />
```

---

## Icon Sizes

| Size Token | Pixels | Usage | Tailwind Class |
|------------|--------|-------|----------------|
| `xs` | 16px | Inline text, badges, dense lists | `w-4 h-4` |
| `sm` | 20px | Buttons with text, form inputs | `w-5 h-5` |
| `md` | 24px | Default, standalone icons | `w-6 h-6` |
| `lg` | 32px | Feature highlights, cards | `w-8 h-8` |

### Size Guidelines

```tsx
// Icon in button (use sm)
<button className="inline-flex items-center gap-2">
  <Search className="w-5 h-5" />
  Search
</button>

// Icon-only button (use md)
<button className="p-2" aria-label="Search">
  <Search className="w-6 h-6" />
</button>

// Icon in navigation (use sm/md)
<nav className="flex items-center gap-4">
  <a className="flex items-center gap-2">
    <Home className="w-5 h-5" />
    Home
  </a>
</nav>

// Feature icon (use lg)
<div className="flex flex-col items-center">
  <div className="w-16 h-16 bg-mereka-teal/10 rounded-full flex items-center justify-center">
    <Palette className="w-8 h-8 text-mereka-teal" />
  </div>
  <span className="mt-2">Arts & Crafts</span>
</div>
```

---

## Icon Colors

### When to Use Which Color

| Context | Color | Tailwind Class | Usage |
|---------|-------|----------------|-------|
| Default | Gray 600 | `text-gray-600` | Standard UI icons |
| Muted | Gray 400 | `text-gray-400` | Placeholder, disabled |
| Interactive | Gray 900 | `text-gray-900` | Clickable icons |
| Primary | Teal | `text-mereka-teal` | Selected, active states |
| Inverse | White | `text-white` | On dark backgrounds |
| Success | Forest | `text-mereka-forest` | Confirmation icons |
| Error | Burgundy | `text-mereka-burgundy` | Error, delete icons |
| Warning | Orange | `text-mereka-orange` | Warning icons |

### Color Usage Examples

```tsx
// Standard (default state)
<MapPin className="w-5 h-5 text-gray-600" />

// Placeholder (no selection)
<Search className="w-5 h-5 text-gray-400" />

// Interactive (button icon)
<button className="hover:text-gray-900">
  <Share2 className="w-5 h-5 text-gray-600 hover:text-gray-900" />
</button>

// Active/Selected
<Heart className="w-5 h-5 text-mereka-teal fill-mereka-teal" />

// Inverse (dark background)
<div className="bg-gray-900 p-4">
  <Bell className="w-5 h-5 text-white" />
</div>

// Status icons
<CheckCircle className="w-5 h-5 text-mereka-forest" />   {/* Success */}
<XCircle className="w-5 h-5 text-mereka-burgundy" />      {/* Error */}
<AlertTriangle className="w-5 h-5 text-mereka-orange" /> {/* Warning */}
```

---

## Experience Type Icons (18)

These icons represent learning categories and experience types on the Mereka platform.

| Category | Icon Name | Lucide Icon | Description |
|----------|-----------|-------------|-------------|
| Food & Beverages | `food-beverages` | `UtensilsCrossed` | Cooking, baking, mixology |
| Hobbies & Entertainment | `hobbies-entertainment` | `Gamepad2` | Games, leisure activities |
| Arts & Crafts | `arts-crafts` | `Palette` | Painting, pottery, crafts |
| Business | `business` | `Briefcase` | Entrepreneurship, marketing |
| Architecture & Urbanism | `architecture-urbanism` | `Building2` | Design, urban planning |
| Design | `design` | `PenTool` | Graphic, UX, product design |
| Art & Culture | `art-culture` | `Theater` | Museums, performances |
| Education | `education` | `GraduationCap` | Teaching, tutoring |
| Digital & IT | `digital-it` | `Laptop` | Coding, digital skills |
| Engineering & Manufacturing | `engineering-manufacturing` | `Cog` | Mechanics, production |
| Nature & Environment | `nature-environment` | `TreeDeciduous` | Ecology, outdoor activities |
| Health & Safety | `health-safety` | `HeartPulse` | First aid, wellness |
| Humanities & Social Sciences | `humanities-social` | `Users` | Psychology, sociology |
| Life Skills | `life-skills` | `Lightbulb` | Personal development |
| Science | `science` | `Microscope` | STEM, research |
| Sports & Physical Activities | `sports-physical` | `Dumbbell` | Fitness, sports training |
| Wellness & Spirituality | `wellness-spirituality` | `Leaf` | Yoga, meditation |
| Social Impact | `social-impact` | `HandHeart` | Volunteering, community |

### Experience Type Icon Component

```tsx
import {
  UtensilsCrossed,
  Gamepad2,
  Palette,
  Briefcase,
  Building2,
  PenTool,
  Theater,
  GraduationCap,
  Laptop,
  Cog,
  TreeDeciduous,
  HeartPulse,
  Users,
  Lightbulb,
  Microscope,
  Dumbbell,
  Leaf,
  HandHeart,
  type LucideIcon,
} from 'lucide-react'

const experienceTypeIcons: Record<string, LucideIcon> = {
  'food-beverages': UtensilsCrossed,
  'hobbies-entertainment': Gamepad2,
  'arts-crafts': Palette,
  'business': Briefcase,
  'architecture-urbanism': Building2,
  'design': PenTool,
  'art-culture': Theater,
  'education': GraduationCap,
  'digital-it': Laptop,
  'engineering-manufacturing': Cog,
  'nature-environment': TreeDeciduous,
  'health-safety': HeartPulse,
  'humanities-social': Users,
  'life-skills': Lightbulb,
  'science': Microscope,
  'sports-physical': Dumbbell,
  'wellness-spirituality': Leaf,
  'social-impact': HandHeart,
}

interface ExperienceTypeIconProps {
  type: keyof typeof experienceTypeIcons
  size?: 'xs' | 'sm' | 'md' | 'lg'
  className?: string
}

const sizeClasses = {
  xs: 'w-4 h-4',
  sm: 'w-5 h-5',
  md: 'w-6 h-6',
  lg: 'w-8 h-8',
}

export function ExperienceTypeIcon({
  type,
  size = 'md',
  className = '',
}: ExperienceTypeIconProps) {
  const Icon = experienceTypeIcons[type]
  if (!Icon) return null

  return <Icon className={`${sizeClasses[size]} ${className}`} />
}
```

### Usage Examples

```tsx
// In a filter chip
<button className="inline-flex items-center gap-2 px-3 py-1.5 rounded-full border border-gray-300">
  <ExperienceTypeIcon type="arts-crafts" size="sm" />
  Arts & Crafts
</button>

// In a category card
<div className="flex flex-col items-center p-6 bg-white rounded-xl shadow-sm">
  <div className="w-16 h-16 bg-mereka-teal/10 rounded-full flex items-center justify-center">
    <ExperienceTypeIcon type="food-beverages" size="lg" className="text-mereka-teal" />
  </div>
  <span className="mt-3 font-heading text-h5">Food & Beverages</span>
  <span className="text-caption text-gray-400">24 experiences</span>
</div>

// In a list item
<li className="flex items-center gap-3 py-2">
  <ExperienceTypeIcon type="education" size="sm" className="text-gray-600" />
  <span>Education</span>
</li>
```

---

## Space Category Icons (10)

These icons represent venue types and space categories for where experiences take place.

| Category | Icon Name | Lucide Icon | Description |
|----------|-----------|-------------|-------------|
| Woodworking Lab | `woodworking-lab` | `Hammer` | Workshop spaces with tools |
| Zoom Room | `zoom-room` | `Video` | Virtual meeting spaces |
| Unusual Spaces | `unusual-spaces` | `Sparkles` | Unique, non-traditional venues |
| Movies | `movies` | `Film` | Screening rooms, cinemas |
| Art and Studio | `art-studio` | `Brush` | Creative studio spaces |
| Collaboration | `collaboration` | `Users` | Team meeting areas |
| Electronics Lab | `electronics-lab` | `Cpu` | Tech & electronics workshops |
| Coworking | `coworking` | `Building` | Shared work spaces |
| Conference Room | `conference-room` | `Presentation` | Formal meeting rooms |
| Reservation Desk | `reservation-desk` | `ClipboardList` | Reception, booking areas |

### Space Category Icon Component

```tsx
import {
  Hammer,
  Video,
  Sparkles,
  Film,
  Brush,
  Users,
  Cpu,
  Building,
  Presentation,
  ClipboardList,
  type LucideIcon,
} from 'lucide-react'

const spaceCategoryIcons: Record<string, LucideIcon> = {
  'woodworking-lab': Hammer,
  'zoom-room': Video,
  'unusual-spaces': Sparkles,
  'movies': Film,
  'art-studio': Brush,
  'collaboration': Users,
  'electronics-lab': Cpu,
  'coworking': Building,
  'conference-room': Presentation,
  'reservation-desk': ClipboardList,
}

interface SpaceCategoryIconProps {
  category: keyof typeof spaceCategoryIcons
  size?: 'xs' | 'sm' | 'md' | 'lg'
  className?: string
}

export function SpaceCategoryIcon({
  category,
  size = 'md',
  className = '',
}: SpaceCategoryIconProps) {
  const Icon = spaceCategoryIcons[category]
  if (!Icon) return null

  return <Icon className={`${sizeClasses[size]} ${className}`} />
}
```

### Usage Examples

```tsx
// Space type badge
<span className="inline-flex items-center gap-1.5 px-2 py-1 bg-gray-100 rounded-md text-body-sm">
  <SpaceCategoryIcon category="art-studio" size="xs" />
  Art Studio
</span>

// Space selector
<div className="grid grid-cols-2 md:grid-cols-5 gap-4">
  {spaceCategories.map((space) => (
    <button
      key={space.id}
      className={`flex flex-col items-center p-4 rounded-lg border ${
        selected === space.id
          ? 'border-mereka-teal bg-mereka-teal/5'
          : 'border-gray-200 hover:border-gray-300'
      }`}
    >
      <SpaceCategoryIcon
        category={space.id}
        size="md"
        className={selected === space.id ? 'text-mereka-teal' : 'text-gray-600'}
      />
      <span className="mt-2 text-body-sm">{space.name}</span>
    </button>
  ))}
</div>
```

---

## Social Login Icons (4)

Custom SVG icons for social authentication providers.

| Provider | Usage | Brand Color |
|----------|-------|-------------|
| Google | Sign in with Google | Multi-color (official) |
| Facebook | Continue with Facebook | `#1877F2` |
| Apple | Sign in with Apple | Black/White |
| Email | Email/Password login | Gray |

### Social Icon Components

```tsx
// Google Icon (multi-color SVG)
export function GoogleIcon({ className = 'w-5 h-5' }: { className?: string }) {
  return (
    <svg className={className} viewBox="0 0 24 24" fill="none">
      <path
        d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"
        fill="#4285F4"
      />
      <path
        d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"
        fill="#34A853"
      />
      <path
        d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"
        fill="#FBBC05"
      />
      <path
        d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"
        fill="#EA4335"
      />
    </svg>
  )
}

// Facebook Icon
export function FacebookIcon({ className = 'w-5 h-5' }: { className?: string }) {
  return (
    <svg className={className} viewBox="0 0 24 24" fill="currentColor">
      <path d="M24 12.073c0-6.627-5.373-12-12-12s-12 5.373-12 12c0 5.99 4.388 10.954 10.125 11.854v-8.385H7.078v-3.47h3.047V9.43c0-3.007 1.792-4.669 4.533-4.669 1.312 0 2.686.235 2.686.235v2.953H15.83c-1.491 0-1.956.925-1.956 1.874v2.25h3.328l-.532 3.47h-2.796v8.385C19.612 23.027 24 18.062 24 12.073z" />
    </svg>
  )
}

// Apple Icon
export function AppleIcon({ className = 'w-5 h-5' }: { className?: string }) {
  return (
    <svg className={className} viewBox="0 0 24 24" fill="currentColor">
      <path d="M17.05 20.28c-.98.95-2.05.8-3.08.35-1.09-.46-2.09-.48-3.24 0-1.44.62-2.2.44-3.06-.35C2.79 15.25 3.51 7.59 9.05 7.31c1.35.07 2.29.74 3.08.8 1.18-.24 2.31-.93 3.57-.84 1.51.12 2.65.72 3.4 1.8-3.12 1.87-2.38 5.98.48 7.13-.57 1.5-1.31 2.99-2.54 4.09l.01-.01zM12.03 7.25c-.15-2.23 1.66-4.07 3.74-4.25.29 2.58-2.34 4.5-3.74 4.25z" />
    </svg>
  )
}

// Email Icon (use Lucide)
import { Mail } from 'lucide-react'
```

### Social Login Buttons

```tsx
// Google (outlined, multi-color icon)
<button className="flex items-center justify-center gap-3 w-full border border-gray-300 rounded-full px-4 py-3 hover:bg-gray-50 transition-colors">
  <GoogleIcon />
  <span className="font-medium">Continue with Google</span>
</button>

// Facebook (filled, brand color)
<button className="flex items-center justify-center gap-3 w-full bg-[#1877F2] text-white rounded-full px-4 py-3 hover:bg-[#166FE5] transition-colors">
  <FacebookIcon />
  <span className="font-medium">Continue with Facebook</span>
</button>

// Apple (filled, black)
<button className="flex items-center justify-center gap-3 w-full bg-black text-white rounded-full px-4 py-3 hover:bg-gray-900 transition-colors">
  <AppleIcon />
  <span className="font-medium">Sign in with Apple</span>
</button>

// Email (outlined)
<button className="flex items-center justify-center gap-3 w-full border border-gray-300 rounded-full px-4 py-3 hover:bg-gray-50 transition-colors">
  <Mail className="w-5 h-5" />
  <span className="font-medium">Continue with Email</span>
</button>
```

### Complete Login Form

```tsx
<div className="space-y-3">
  <button className="flex items-center justify-center gap-3 w-full border border-gray-300 rounded-full px-4 py-3 hover:bg-gray-50">
    <GoogleIcon />
    <span>Continue with Google</span>
  </button>

  <button className="flex items-center justify-center gap-3 w-full bg-[#1877F2] text-white rounded-full px-4 py-3 hover:bg-[#166FE5]">
    <FacebookIcon />
    <span>Continue with Facebook</span>
  </button>

  <button className="flex items-center justify-center gap-3 w-full bg-black text-white rounded-full px-4 py-3 hover:bg-gray-900">
    <AppleIcon />
    <span>Sign in with Apple</span>
  </button>

  <div className="flex items-center gap-4 my-4">
    <hr className="flex-1 border-gray-200" />
    <span className="text-caption text-gray-400">or</span>
    <hr className="flex-1 border-gray-200" />
  </div>

  <button className="flex items-center justify-center gap-3 w-full border border-gray-300 rounded-full px-4 py-3 hover:bg-gray-50">
    <Mail className="w-5 h-5" />
    <span>Continue with Email</span>
  </button>
</div>
```

---

## UI Icons

Common icons used throughout the Mereka interface, organized by function.

### Navigation Icons

| Icon | Lucide Name | Usage |
|------|-------------|-------|
| Menu | `Menu` | Mobile hamburger menu |
| Close | `X` | Close dialogs, menus |
| Chevron Left | `ChevronLeft` | Back navigation |
| Chevron Right | `ChevronRight` | Forward, expand |
| Chevron Down | `ChevronDown` | Dropdowns, accordions |
| Chevron Up | `ChevronUp` | Collapse, scroll up |
| Arrow Left | `ArrowLeft` | Previous step |
| Arrow Right | `ArrowRight` | Next step, CTAs |
| Home | `Home` | Home navigation |
| External Link | `ExternalLink` | Opens in new tab |

```tsx
import {
  Menu,
  X,
  ChevronLeft,
  ChevronRight,
  ChevronDown,
  ChevronUp,
  ArrowLeft,
  ArrowRight,
  Home,
  ExternalLink,
} from 'lucide-react'

// Mobile menu toggle
<button aria-label="Open menu" className="p-2 md:hidden">
  <Menu className="w-6 h-6" />
</button>

// Back button
<button className="inline-flex items-center gap-2 text-gray-600 hover:text-gray-900">
  <ChevronLeft className="w-5 h-5" />
  Back
</button>

// Dropdown trigger
<button className="inline-flex items-center gap-1">
  Filter
  <ChevronDown className="w-4 h-4" />
</button>

// CTA with arrow
<button className="inline-flex items-center gap-2 bg-gray-900 text-white px-4 py-2 rounded-full">
  Continue
  <ArrowRight className="w-4 h-4" />
</button>
```

### Action Icons

| Icon | Lucide Name | Usage |
|------|-------------|-------|
| Search | `Search` | Search bars, search CTAs |
| Filter | `SlidersHorizontal` | Filter controls |
| Heart | `Heart` | Save/favorite |
| Share | `Share2` | Share content |
| Edit | `Pencil` | Edit content |
| Delete | `Trash2` | Delete actions |
| Plus | `Plus` | Add new item |
| Minus | `Minus` | Remove, decrease |
| Copy | `Copy` | Copy to clipboard |
| Download | `Download` | Download files |
| Upload | `Upload` | Upload files |
| Refresh | `RefreshCw` | Refresh, reload |

```tsx
import {
  Search,
  SlidersHorizontal,
  Heart,
  Share2,
  Pencil,
  Trash2,
  Plus,
  Minus,
  Copy,
  Download,
  Upload,
  RefreshCw,
} from 'lucide-react'

// Search input
<div className="flex items-center gap-2 border border-gray-300 rounded-full px-4 py-2">
  <Search className="w-5 h-5 text-gray-400" />
  <input type="text" placeholder="Search..." className="flex-1 outline-none" />
</div>

// Filter button
<button className="inline-flex items-center gap-2 px-3 py-1.5 border border-gray-300 rounded-full">
  <SlidersHorizontal className="w-4 h-4" />
  Filters
</button>

// Save button (toggleable)
<button aria-label="Save to favorites" aria-pressed={isSaved}>
  <Heart
    className={`w-5 h-5 ${isSaved ? 'text-mereka-magenta fill-mereka-magenta' : 'text-gray-600'}`}
  />
</button>

// Action menu icons
<DropdownMenu.Item>
  <Pencil className="w-4 h-4 mr-2" />
  Edit
</DropdownMenu.Item>
<DropdownMenu.Item className="text-red-600">
  <Trash2 className="w-4 h-4 mr-2" />
  Delete
</DropdownMenu.Item>

// Add button
<button className="inline-flex items-center gap-2">
  <Plus className="w-4 h-4" />
  Add Experience
</button>

// Quantity control
<div className="inline-flex items-center gap-2">
  <button className="p-1 rounded-full border border-gray-300">
    <Minus className="w-4 h-4" />
  </button>
  <span className="w-8 text-center">2</span>
  <button className="p-1 rounded-full border border-gray-300">
    <Plus className="w-4 h-4" />
  </button>
</div>
```

### Status Icons

| Icon | Lucide Name | Color | Usage |
|------|-------------|-------|-------|
| Check | `Check` | Green | Success, complete |
| Check Circle | `CheckCircle` | Green | Verified, confirmed |
| X | `X` | Red | Remove, close |
| X Circle | `XCircle` | Red | Error, failed |
| Alert Triangle | `AlertTriangle` | Orange | Warning |
| Alert Circle | `AlertCircle` | Orange | Caution |
| Info | `Info` | Blue | Information |
| Help Circle | `HelpCircle` | Gray | Help, tooltip |
| Loader | `Loader2` | Gray | Loading spinner |

```tsx
import {
  Check,
  CheckCircle,
  X,
  XCircle,
  AlertTriangle,
  AlertCircle,
  Info,
  HelpCircle,
  Loader2,
} from 'lucide-react'

// Success message
<div className="flex items-center gap-2 bg-green-50 text-green-700 px-4 py-3 rounded-lg">
  <CheckCircle className="w-5 h-5" />
  Booking confirmed successfully!
</div>

// Error message
<div className="flex items-center gap-2 bg-red-50 text-red-700 px-4 py-3 rounded-lg">
  <XCircle className="w-5 h-5" />
  Payment failed. Please try again.
</div>

// Warning message
<div className="flex items-center gap-2 bg-yellow-50 text-yellow-700 px-4 py-3 rounded-lg">
  <AlertTriangle className="w-5 h-5" />
  Your session will expire in 5 minutes.
</div>

// Info message
<div className="flex items-center gap-2 bg-blue-50 text-blue-700 px-4 py-3 rounded-lg">
  <Info className="w-5 h-5" />
  Cancellation is free up to 24 hours before.
</div>

// Help tooltip trigger
<button aria-label="Help">
  <HelpCircle className="w-4 h-4 text-gray-400" />
</button>

// Loading spinner
<button disabled className="inline-flex items-center gap-2">
  <Loader2 className="w-4 h-4 animate-spin" />
  Processing...
</button>

// List with checkmarks
<ul className="space-y-2">
  <li className="flex items-center gap-2">
    <Check className="w-4 h-4 text-mereka-forest" />
    Free cancellation
  </li>
  <li className="flex items-center gap-2">
    <Check className="w-4 h-4 text-mereka-forest" />
    Instant confirmation
  </li>
</ul>
```

### Object Icons

| Icon | Lucide Name | Usage |
|------|-------------|-------|
| Calendar | `Calendar` | Dates, scheduling |
| Clock | `Clock` | Time, duration |
| Map Pin | `MapPin` | Location |
| User | `User` | User profile |
| Users | `Users` | Group, participants |
| Building | `Building2` | Hub, organization |
| Mail | `Mail` | Email |
| Phone | `Phone` | Phone number |
| Bell | `Bell` | Notifications |
| Settings | `Settings` | Settings, preferences |
| Star | `Star` | Rating, favorites |
| Tag | `Tag` | Labels, categories |
| Bookmark | `Bookmark` | Save for later |
| Camera | `Camera` | Photos, images |
| Credit Card | `CreditCard` | Payment |
| Receipt | `Receipt` | Invoice, receipt |
| Globe | `Globe` | Language, website |
| Link | `Link` | URL, connection |

```tsx
import {
  Calendar,
  Clock,
  MapPin,
  User,
  Users,
  Building2,
  Mail,
  Phone,
  Bell,
  Settings,
  Star,
  Tag,
  Bookmark,
  Camera,
  CreditCard,
  Receipt,
  Globe,
  Link,
} from 'lucide-react'

// Experience meta info
<div className="flex items-center gap-4 text-body-sm text-gray-600">
  <span className="flex items-center gap-1">
    <Calendar className="w-4 h-4" />
    Sat, Jan 20
  </span>
  <span className="flex items-center gap-1">
    <Clock className="w-4 h-4" />
    2 hours
  </span>
  <span className="flex items-center gap-1">
    <MapPin className="w-4 h-4" />
    Kuala Lumpur
  </span>
</div>

// User menu
<div className="flex items-center gap-2">
  <User className="w-5 h-5" />
  Profile
</div>

// Participants count
<div className="flex items-center gap-1 text-body-sm">
  <Users className="w-4 h-4 text-gray-400" />
  <span>4/10 spots</span>
</div>

// Rating display
<div className="flex items-center gap-1">
  <Star className="w-4 h-4 text-yellow-400 fill-yellow-400" />
  <span className="font-medium">4.8</span>
  <span className="text-gray-400">(24 reviews)</span>
</div>

// Notification badge
<button className="relative p-2">
  <Bell className="w-5 h-5" />
  <span className="absolute -top-1 -right-1 w-4 h-4 bg-red-500 text-white text-xs rounded-full flex items-center justify-center">
    3
  </span>
</button>

// Contact info
<div className="space-y-2 text-body-sm">
  <a className="flex items-center gap-2 text-gray-600 hover:text-gray-900">
    <Mail className="w-4 h-4" />
    hello@mereka.io
  </a>
  <a className="flex items-center gap-2 text-gray-600 hover:text-gray-900">
    <Phone className="w-4 h-4" />
    +60 12-345 6789
  </a>
</div>
```

---

## Accessibility

### Icon Buttons

All icon-only buttons MUST have accessible labels.

```tsx
// BAD - No accessible label
<button>
  <Heart className="w-5 h-5" />
</button>

// GOOD - Using aria-label
<button aria-label="Save to favorites">
  <Heart className="w-5 h-5" />
</button>

// GOOD - Using sr-only text
<button>
  <Heart className="w-5 h-5" aria-hidden="true" />
  <span className="sr-only">Save to favorites</span>
</button>
```

### Decorative vs Informative Icons

```tsx
// Decorative icon (next to text) - hide from screen readers
<button>
  <Search className="w-4 h-4" aria-hidden="true" />
  Search
</button>

// Informative icon (conveys meaning) - provide label
<span className="flex items-center gap-1">
  <CheckCircle className="w-4 h-4 text-green-500" aria-hidden="true" />
  <span className="sr-only">Status:</span>
  Confirmed
</span>
```

### Focus States

```tsx
// Icon button with focus ring
<button className="p-2 rounded-full focus:outline-none focus:ring-2 focus:ring-mereka-teal focus:ring-offset-2">
  <Settings className="w-5 h-5" />
</button>
```

### Animated Icons

```tsx
// Loading spinner - announce to screen readers
<button disabled aria-busy="true">
  <Loader2 className="w-4 h-4 animate-spin" aria-hidden="true" />
  <span className="sr-only">Loading</span>
  Processing...
</button>
```

---

## Icon Component with Accessibility

```tsx
import { type LucideIcon } from 'lucide-react'

interface IconProps {
  icon: LucideIcon
  size?: 'xs' | 'sm' | 'md' | 'lg'
  label?: string
  className?: string
}

const sizeClasses = {
  xs: 'w-4 h-4',
  sm: 'w-5 h-5',
  md: 'w-6 h-6',
  lg: 'w-8 h-8',
}

export function Icon({ icon: IconComponent, size = 'md', label, className = '' }: IconProps) {
  const iconClasses = `${sizeClasses[size]} ${className}`

  if (label) {
    return (
      <>
        <IconComponent className={iconClasses} aria-hidden="true" />
        <span className="sr-only">{label}</span>
      </>
    )
  }

  return <IconComponent className={iconClasses} aria-hidden="true" />
}

// Usage
<Icon icon={Heart} size="sm" label="Save to favorites" className="text-gray-600" />
```

---

## Don'ts

- **Don't use raw SVGs inline** - Use Lucide or the component wrappers
- **Don't create custom icon sizes** - Stick to xs (16), sm (20), md (24), lg (32)
- **Don't use icons without accessible labels** - Icon-only buttons need aria-label or sr-only text
- **Don't use icons that don't match** - Stick to Lucide for UI, custom SVGs for brands only
- **Don't animate icons excessively** - Only use animation for loading states
- **Don't use filled icons inconsistently** - Fill only for active/selected states (heart, star)
- **Don't mix icon libraries** - Lucide only (no mixing with Heroicons, Feather, etc.)

---

## Quick Reference

### Import All Common Icons

```tsx
import {
  // Navigation
  Menu, X, ChevronLeft, ChevronRight, ChevronDown, ChevronUp,
  ArrowLeft, ArrowRight, Home, ExternalLink,

  // Actions
  Search, SlidersHorizontal, Heart, Share2, Pencil, Trash2,
  Plus, Minus, Copy, Download, Upload, RefreshCw,

  // Status
  Check, CheckCircle, XCircle, AlertTriangle, AlertCircle,
  Info, HelpCircle, Loader2,

  // Objects
  Calendar, Clock, MapPin, User, Users, Building2, Mail, Phone,
  Bell, Settings, Star, Tag, Bookmark, Camera, CreditCard, Receipt,

  // Experience Types
  UtensilsCrossed, Gamepad2, Palette, Briefcase, Building2, PenTool,
  Theater, GraduationCap, Laptop, Cog, TreeDeciduous, HeartPulse,
  Lightbulb, Microscope, Dumbbell, Leaf, HandHeart,

  // Space Categories
  Hammer, Video, Sparkles, Film, Brush, Cpu, Presentation, ClipboardList,
} from 'lucide-react'
```

### Size Quick Reference

| Token | Pixels | Class | Use Case |
|-------|--------|-------|----------|
| xs | 16px | `w-4 h-4` | Dense UI, badges |
| sm | 20px | `w-5 h-5` | Buttons, inputs |
| md | 24px | `w-6 h-6` | Default, standalone |
| lg | 32px | `w-8 h-8` | Feature cards |
