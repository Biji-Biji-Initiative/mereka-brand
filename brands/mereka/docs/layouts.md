# Layouts

> Page layouts and structural patterns across the Mereka application ecosystem.

**Apps**: mereka.io (public), app.mereka.io (app), checkout.mereka.io (checkout), admin.mereka.io (admin)

---

## Container Widths

Mereka uses three primary container widths to create focus hierarchy.

| Width | Class | Usage |
|-------|-------|-------|
| **Full** | `max-w-7xl` (1280px) | Browse pages, dashboards, data-heavy layouts |
| **Standard** | `max-w-5xl` (1024px) | Detail pages, forms, content pages |
| **Narrow** | `max-w-2xl` (672px) | Checkout, focused forms, auth pages |
| **Compact** | `max-w-md` (448px) | Modals, single-field forms |

### Base Container

```tsx
interface ContainerProps {
  size?: 'full' | 'standard' | 'narrow' | 'compact'
  children: React.ReactNode
  className?: string
}

const containerSizes = {
  full: 'max-w-7xl',
  standard: 'max-w-5xl',
  narrow: 'max-w-2xl',
  compact: 'max-w-md',
}

export function Container({ size = 'full', children, className }: ContainerProps) {
  return (
    <div className={`mx-auto px-4 sm:px-6 lg:px-8 ${containerSizes[size]} ${className}`}>
      {children}
    </div>
  )
}
```

---

## Grid System

Mereka uses a 12-column grid with responsive breakpoints.

### Grid Component

```tsx
interface GridProps {
  cols?: 1 | 2 | 3 | 4 | 6 | 12
  gap?: 'sm' | 'md' | 'lg'
  children: React.ReactNode
  className?: string
}

const gridCols = {
  1: 'grid-cols-1',
  2: 'grid-cols-1 sm:grid-cols-2',
  3: 'grid-cols-1 sm:grid-cols-2 lg:grid-cols-3',
  4: 'grid-cols-1 sm:grid-cols-2 lg:grid-cols-4',
  6: 'grid-cols-2 sm:grid-cols-3 lg:grid-cols-6',
  12: 'grid-cols-4 sm:grid-cols-6 lg:grid-cols-12',
}

const gridGaps = {
  sm: 'gap-4',
  md: 'gap-6',
  lg: 'gap-8',
}

export function Grid({ cols = 3, gap = 'md', children, className }: GridProps) {
  return (
    <div className={`grid ${gridCols[cols]} ${gridGaps[gap]} ${className}`}>
      {children}
    </div>
  )
}
```

### Usage

```tsx
// Experience cards - 3 columns
<Grid cols={3} gap="md">
  {experiences.map(exp => <ExperienceCard key={exp.id} experience={exp} />)}
</Grid>

// Hub cards - 2 columns
<Grid cols={2} gap="lg">
  {hubs.map(hub => <HubCard key={hub.id} hub={hub} />)}
</Grid>
```

---

## 1. Public Pages (mereka.io)

Public-facing marketing and discovery pages.

### Landing Page Layout

Full-width hero with contained content sections.

```tsx
export function LandingLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="min-h-screen flex flex-col">
      {/* Public Header */}
      <header className="sticky top-0 z-30 bg-white border-b border-gray-200">
        <Container size="full" className="h-16 flex items-center justify-between">
          <a href="/">
            <MerekaLogo className="h-8" />
          </a>

          <nav className="hidden md:flex items-center gap-6">
            <a href="/experiences" className="text-body text-gray-600 hover:text-gray-900">
              Experiences
            </a>
            <a href="/hubs" className="text-body text-gray-600 hover:text-gray-900">
              Hubs
            </a>
            <a href="/experts" className="text-body text-gray-600 hover:text-gray-900">
              Experts
            </a>
          </nav>

          <div className="flex items-center gap-3">
            <Button variant="ghost">Log in</Button>
            <Button variant="solid">Sign up</Button>
          </div>
        </Container>
      </header>

      {/* Main Content */}
      <main className="flex-1">
        {children}
      </main>

      {/* Footer */}
      <Footer />
    </div>
  )
}
```

#### Hero Section

```tsx
export function HeroSection() {
  return (
    <section className="relative bg-gradient-to-b from-gray-50 to-white py-16 md:py-24">
      <Container size="full">
        <div className="text-center max-w-3xl mx-auto">
          <h1 className="font-heading text-display text-gray-900">
            Discover experiences that inspire
          </h1>
          <p className="text-body-lg text-gray-600 mt-4">
            Connect with local experts and learn something new today.
          </p>

          {/* Search Bar */}
          <div className="mt-8 max-w-2xl mx-auto">
            <SearchBar size="lg" />
          </div>

          {/* Quick Filters */}
          <div className="flex flex-wrap justify-center gap-2 mt-6">
            {['Workshops', 'Online', 'This Weekend', 'Near Me'].map(filter => (
              <Button key={filter} variant="outline" size="small">
                {filter}
              </Button>
            ))}
          </div>
        </div>
      </Container>
    </section>
  )
}
```

#### Content Sections

```tsx
export function ContentSection({
  title,
  subtitle,
  children,
  action,
}: {
  title: string
  subtitle?: string
  children: React.ReactNode
  action?: { label: string; href: string }
}) {
  return (
    <section className="py-12 md:py-16">
      <Container size="full">
        <div className="flex items-end justify-between mb-8">
          <div>
            <h2 className="font-heading text-h1 text-gray-900">{title}</h2>
            {subtitle && (
              <p className="text-body text-gray-600 mt-1">{subtitle}</p>
            )}
          </div>
          {action && (
            <Button variant="ghost" asChild>
              <a href={action.href}>
                {action.label}
                <ArrowRightIcon className="ml-2 w-4 h-4" />
              </a>
            </Button>
          )}
        </div>
        {children}
      </Container>
    </section>
  )
}
```

### Browse/Search Results Layout

Grid-based listing with filters sidebar.

```tsx
export function BrowseLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="min-h-screen flex flex-col">
      <PublicHeader />

      <main className="flex-1 bg-gray-50">
        <Container size="full" className="py-8">
          {children}
        </Container>
      </main>

      <Footer />
    </div>
  )
}

export function BrowsePage() {
  const [filtersOpen, setFiltersOpen] = useState(false)

  return (
    <BrowseLayout>
      {/* Search Header */}
      <div className="mb-8">
        <h1 className="font-heading text-h1 text-gray-900">Experiences</h1>
        <p className="text-body text-gray-600 mt-1">
          {resultCount} experiences found
        </p>
      </div>

      {/* Filter Bar */}
      <div className="flex items-center gap-4 mb-6 pb-6 border-b border-gray-200">
        <Button
          variant="outline"
          onClick={() => setFiltersOpen(!filtersOpen)}
          className="md:hidden"
        >
          <FilterIcon className="w-4 h-4 mr-2" />
          Filters
        </Button>

        {/* Desktop Inline Filters */}
        <div className="hidden md:flex items-center gap-4 flex-1">
          <Select placeholder="Category">
            <Select.Option value="workshop">Workshop</Select.Option>
            <Select.Option value="tour">Tour</Select.Option>
            <Select.Option value="class">Class</Select.Option>
          </Select>

          <Select placeholder="Location">
            <Select.Option value="kl">Kuala Lumpur</Select.Option>
            <Select.Option value="penang">Penang</Select.Option>
            <Select.Option value="online">Online</Select.Option>
          </Select>

          <Select placeholder="Date">
            <Select.Option value="today">Today</Select.Option>
            <Select.Option value="week">This Week</Select.Option>
            <Select.Option value="month">This Month</Select.Option>
          </Select>

          <Select placeholder="Price">
            <Select.Option value="free">Free</Select.Option>
            <Select.Option value="under50">Under RM 50</Select.Option>
            <Select.Option value="under100">Under RM 100</Select.Option>
          </Select>
        </div>

        {/* Sort */}
        <Select placeholder="Sort by" className="ml-auto">
          <Select.Option value="relevance">Relevance</Select.Option>
          <Select.Option value="date">Date</Select.Option>
          <Select.Option value="price-low">Price: Low to High</Select.Option>
          <Select.Option value="price-high">Price: High to Low</Select.Option>
          <Select.Option value="rating">Rating</Select.Option>
        </Select>
      </div>

      {/* Results Grid */}
      <div className="flex gap-8">
        {/* Sidebar Filters (Desktop) */}
        <aside className="hidden lg:block w-64 shrink-0">
          <FiltersSidebar />
        </aside>

        {/* Results */}
        <div className="flex-1">
          <Grid cols={3} gap="md">
            {experiences.map(exp => (
              <ExperienceCard key={exp.id} experience={exp} />
            ))}
          </Grid>

          {/* Pagination */}
          <div className="flex justify-center mt-12">
            <Pagination
              currentPage={page}
              totalPages={totalPages}
              onPageChange={setPage}
            />
          </div>
        </div>
      </div>

      {/* Mobile Filters Drawer */}
      <Drawer open={filtersOpen} onOpenChange={setFiltersOpen}>
        <Drawer.Content>
          <Drawer.Header>
            <Drawer.Title>Filters</Drawer.Title>
          </Drawer.Header>
          <FiltersSidebar />
          <Drawer.Footer>
            <Button variant="outline" onClick={() => setFiltersOpen(false)}>
              Clear All
            </Button>
            <Button variant="solid" onClick={() => setFiltersOpen(false)}>
              Apply Filters
            </Button>
          </Drawer.Footer>
        </Drawer.Content>
      </Drawer>
    </BrowseLayout>
  )
}
```

#### Filters Sidebar Component

```tsx
function FiltersSidebar() {
  return (
    <div className="space-y-6">
      {/* Category */}
      <FilterGroup title="Category">
        <Checkbox label="Workshop" />
        <Checkbox label="Tour" />
        <Checkbox label="Class" />
        <Checkbox label="Retreat" />
        <Checkbox label="Experience" />
      </FilterGroup>

      {/* Location Type */}
      <FilterGroup title="Location">
        <Radio name="location" label="All" value="all" />
        <Radio name="location" label="In-Person" value="physical" />
        <Radio name="location" label="Online" value="virtual" />
        <Radio name="location" label="Hybrid" value="hybrid" />
      </FilterGroup>

      {/* Price Range */}
      <FilterGroup title="Price">
        <RangeSlider min={0} max={500} step={10} />
      </FilterGroup>

      {/* Duration */}
      <FilterGroup title="Duration">
        <Checkbox label="Under 1 hour" />
        <Checkbox label="1-3 hours" />
        <Checkbox label="Half day" />
        <Checkbox label="Full day" />
        <Checkbox label="Multi-day" />
      </FilterGroup>

      {/* Rating */}
      <FilterGroup title="Rating">
        <Radio name="rating" label="Any" value="any" />
        <Radio name="rating" label="4+ stars" value="4" />
        <Radio name="rating" label="4.5+ stars" value="4.5" />
      </FilterGroup>
    </div>
  )
}

function FilterGroup({ title, children }: { title: string; children: React.ReactNode }) {
  return (
    <div className="pb-6 border-b border-gray-200 last:border-0">
      <h3 className="font-heading text-h5 text-gray-900 mb-4">{title}</h3>
      <div className="space-y-3">
        {children}
      </div>
    </div>
  )
}
```

### Detail Page Layout

Focused content with sidebar booking widget.

```tsx
export function DetailLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="min-h-screen flex flex-col">
      <PublicHeader />

      <main className="flex-1 bg-white">
        {children}
      </main>

      <Footer />
    </div>
  )
}

export function ExperienceDetailPage({ experience }: { experience: Experience }) {
  return (
    <DetailLayout>
      {/* Hero Image */}
      <div className="aspect-[21/9] relative bg-gray-100">
        <img
          src={experience.coverImage}
          alt={experience.title}
          className="w-full h-full object-cover"
        />
      </div>

      <Container size="standard" className="py-8 md:py-12">
        <div className="lg:flex lg:gap-12">
          {/* Main Content */}
          <div className="flex-1 min-w-0">
            {/* Breadcrumb */}
            <nav className="text-caption text-gray-400 mb-4">
              <a href="/experiences" className="hover:text-gray-600">Experiences</a>
              <span className="mx-2">/</span>
              <span>{experience.category}</span>
            </nav>

            {/* Title & Host */}
            <div className="flex items-start justify-between">
              <div>
                <h1 className="font-heading text-display-sm text-gray-900">
                  {experience.title}
                </h1>
                <div className="flex items-center gap-3 mt-3">
                  <Avatar src={experience.host.avatar} alt={experience.host.name} size="sm" />
                  <div>
                    <p className="text-body font-medium">Hosted by {experience.host.name}</p>
                    {experience.rating && (
                      <div className="flex items-center gap-1 text-body-sm">
                        <StarIcon className="w-4 h-4 text-yellow-400 fill-yellow-400" />
                        <span>{experience.rating}</span>
                        <span className="text-gray-400">({experience.reviewCount} reviews)</span>
                      </div>
                    )}
                  </div>
                </div>
              </div>

              {/* Actions */}
              <div className="flex items-center gap-2">
                <Button variant="ghost" size="icon" aria-label="Save">
                  <HeartIcon />
                </Button>
                <Button variant="ghost" size="icon" aria-label="Share">
                  <ShareIcon />
                </Button>
              </div>
            </div>

            {/* Quick Info */}
            <div className="flex flex-wrap gap-4 mt-6 py-6 border-y border-gray-200">
              <div className="flex items-center gap-2">
                <CalendarIcon className="w-5 h-5 text-gray-400" />
                <span className="text-body">{formatDate(experience.date)}</span>
              </div>
              <div className="flex items-center gap-2">
                <ClockIcon className="w-5 h-5 text-gray-400" />
                <span className="text-body">{experience.duration}</span>
              </div>
              <div className="flex items-center gap-2">
                <LocationIcon className="w-5 h-5 text-gray-400" />
                <span className="text-body">
                  {experience.location.type === 'virtual' ? 'Online' : experience.location.address}
                </span>
              </div>
              <div className="flex items-center gap-2">
                <UsersIcon className="w-5 h-5 text-gray-400" />
                <span className="text-body">{experience.maxParticipants} max</span>
              </div>
            </div>

            {/* Description */}
            <section className="py-8">
              <h2 className="font-heading text-h2 text-gray-900 mb-4">About this experience</h2>
              <div className="prose prose-gray max-w-none">
                {experience.description}
              </div>
            </section>

            {/* What's Included */}
            <section className="py-8 border-t border-gray-200">
              <h2 className="font-heading text-h2 text-gray-900 mb-4">What's included</h2>
              <ul className="grid grid-cols-2 gap-3">
                {experience.includes.map((item, i) => (
                  <li key={i} className="flex items-center gap-2 text-body">
                    <CheckIcon className="w-5 h-5 text-mereka-teal" />
                    {item}
                  </li>
                ))}
              </ul>
            </section>

            {/* Host Section */}
            <section className="py-8 border-t border-gray-200">
              <h2 className="font-heading text-h2 text-gray-900 mb-4">Meet your host</h2>
              <div className="flex gap-4">
                <Avatar src={experience.host.avatar} alt={experience.host.name} size="xl" />
                <div>
                  <h3 className="font-heading text-h3">{experience.host.name}</h3>
                  <p className="text-body-sm text-gray-600 mt-1">{experience.host.bio}</p>
                  <Button variant="outline" className="mt-4">Contact Host</Button>
                </div>
              </div>
            </section>

            {/* Reviews */}
            <section className="py-8 border-t border-gray-200">
              <div className="flex items-center justify-between mb-6">
                <h2 className="font-heading text-h2 text-gray-900">Reviews</h2>
                <Button variant="ghost">View all</Button>
              </div>
              <div className="space-y-6">
                {experience.reviews.slice(0, 3).map(review => (
                  <ReviewCard key={review.id} review={review} />
                ))}
              </div>
            </section>
          </div>

          {/* Booking Sidebar */}
          <aside className="hidden lg:block w-96 shrink-0">
            <div className="sticky top-24">
              <BookingWidget experience={experience} />
            </div>
          </aside>
        </div>
      </Container>

      {/* Mobile Booking Bar */}
      <div className="lg:hidden sticky bottom-0 bg-white border-t border-gray-200 p-4">
        <div className="flex items-center justify-between">
          <div>
            <span className="text-caption text-gray-400">From</span>
            <p className="font-heading text-h3 text-gray-900">
              {experience.currency} {experience.price}
            </p>
          </div>
          <Button variant="solid" size="default">
            Check Availability
          </Button>
        </div>
      </div>
    </DetailLayout>
  )
}
```

#### Booking Widget Component

```tsx
function BookingWidget({ experience }: { experience: Experience }) {
  return (
    <div className="bg-white rounded-xl border border-gray-200 shadow-lg p-6">
      {/* Price */}
      <div className="flex items-baseline gap-2 mb-6">
        <span className="font-heading text-h2 text-gray-900">
          {experience.currency} {experience.price}
        </span>
        <span className="text-body text-gray-400">/ person</span>
      </div>

      {/* Date Selection */}
      <div className="space-y-4 mb-6">
        <div>
          <label className="block text-body-sm font-medium text-gray-700 mb-2">
            Select Date
          </label>
          <DatePicker
            availableDates={experience.availableDates}
            onSelect={setSelectedDate}
          />
        </div>

        <div>
          <label className="block text-body-sm font-medium text-gray-700 mb-2">
            Number of Guests
          </label>
          <Select value={guests} onChange={setGuests}>
            {[1, 2, 3, 4, 5].map(n => (
              <Select.Option key={n} value={n}>{n} guest{n > 1 ? 's' : ''}</Select.Option>
            ))}
          </Select>
        </div>
      </div>

      {/* Book Button */}
      <Button variant="solid" className="w-full mb-4">
        Reserve
      </Button>

      <p className="text-caption text-center text-gray-400">
        You won't be charged yet
      </p>

      {/* Price Breakdown */}
      <div className="mt-6 pt-6 border-t border-gray-200 space-y-3">
        <div className="flex justify-between text-body">
          <span>{experience.currency} {experience.price} x {guests} guests</span>
          <span>{experience.currency} {experience.price * guests}</span>
        </div>
        <div className="flex justify-between text-body">
          <span>Service fee</span>
          <span>{experience.currency} {serviceFee}</span>
        </div>
        <div className="flex justify-between font-heading text-h4 pt-3 border-t border-gray-200">
          <span>Total</span>
          <span>{experience.currency} {total}</span>
        </div>
      </div>
    </div>
  )
}
```

### Hub Detail Layout

```tsx
export function HubDetailPage({ hub }: { hub: Hub }) {
  return (
    <DetailLayout>
      {/* Cover Image */}
      <div className="h-48 md:h-64 relative bg-gray-100">
        <img
          src={hub.coverImage}
          alt={hub.name}
          className="w-full h-full object-cover"
        />
      </div>

      <Container size="standard" className="py-8 md:py-12">
        {/* Hub Header */}
        <div className="flex flex-col md:flex-row gap-6 -mt-16 md:-mt-20 relative z-10">
          <Avatar src={hub.logo} alt={hub.name} size="2xl" className="border-4 border-white shadow-lg" />
          <div className="flex-1">
            <h1 className="font-heading text-display-sm text-gray-900">{hub.name}</h1>
            <div className="flex items-center gap-4 mt-2 text-body text-gray-600">
              <span className="flex items-center gap-1">
                <LocationIcon className="w-4 h-4" />
                {hub.location}
              </span>
              <span>{hub.experienceCount} experiences</span>
              {hub.rating && (
                <span className="flex items-center gap-1">
                  <StarIcon className="w-4 h-4 text-yellow-400 fill-yellow-400" />
                  {hub.rating}
                </span>
              )}
            </div>
            <p className="text-body text-gray-600 mt-4 max-w-2xl">{hub.description}</p>
          </div>
          <Button variant="solid">Follow</Button>
        </div>

        {/* Tabs */}
        <Tabs defaultValue="experiences" className="mt-12">
          <Tabs.List>
            <Tabs.Trigger value="experiences">Experiences</Tabs.Trigger>
            <Tabs.Trigger value="about">About</Tabs.Trigger>
            <Tabs.Trigger value="team">Team</Tabs.Trigger>
            <Tabs.Trigger value="reviews">Reviews</Tabs.Trigger>
          </Tabs.List>

          <Tabs.Content value="experiences" className="mt-8">
            <Grid cols={3} gap="md">
              {hub.experiences.map(exp => (
                <ExperienceCard key={exp.id} experience={exp} />
              ))}
            </Grid>
          </Tabs.Content>

          <Tabs.Content value="about" className="mt-8">
            <div className="prose prose-gray max-w-2xl">
              {hub.about}
            </div>
          </Tabs.Content>

          <Tabs.Content value="team" className="mt-8">
            <Grid cols={4} gap="md">
              {hub.team.map(member => (
                <TeamMemberCard key={member.id} member={member} />
              ))}
            </Grid>
          </Tabs.Content>

          <Tabs.Content value="reviews" className="mt-8">
            <div className="space-y-6 max-w-2xl">
              {hub.reviews.map(review => (
                <ReviewCard key={review.id} review={review} />
              ))}
            </div>
          </Tabs.Content>
        </Tabs>
      </Container>
    </DetailLayout>
  )
}
```

### Expert Profile Layout

```tsx
export function ExpertDetailPage({ expert }: { expert: Expert }) {
  return (
    <DetailLayout>
      <Container size="standard" className="py-8 md:py-12">
        <div className="lg:flex lg:gap-12">
          {/* Main Content */}
          <div className="flex-1">
            {/* Profile Header */}
            <div className="flex gap-6">
              <Avatar src={expert.avatar} alt={expert.name} size="2xl" />
              <div>
                <h1 className="font-heading text-display-sm text-gray-900">{expert.name}</h1>
                <p className="text-body-lg text-gray-600 mt-1">{expert.title}</p>
                <div className="flex items-center gap-4 mt-3 text-body-sm text-gray-400">
                  <span className="flex items-center gap-1">
                    <LocationIcon className="w-4 h-4" />
                    {expert.location}
                  </span>
                  <span>{expert.experienceCount} experiences</span>
                  {expert.rating && (
                    <span className="flex items-center gap-1">
                      <StarIcon className="w-4 h-4 text-yellow-400 fill-yellow-400" />
                      {expert.rating}
                    </span>
                  )}
                </div>
              </div>
            </div>

            {/* Bio */}
            <section className="py-8 border-b border-gray-200">
              <h2 className="font-heading text-h2 text-gray-900 mb-4">About</h2>
              <p className="text-body text-gray-600">{expert.bio}</p>
            </section>

            {/* Expertise */}
            <section className="py-8 border-b border-gray-200">
              <h2 className="font-heading text-h2 text-gray-900 mb-4">Expertise</h2>
              <div className="flex flex-wrap gap-2">
                {expert.expertise.map(skill => (
                  <Tag key={skill} variant="standard">{skill}</Tag>
                ))}
              </div>
            </section>

            {/* Experiences */}
            <section className="py-8">
              <h2 className="font-heading text-h2 text-gray-900 mb-6">Experiences</h2>
              <Grid cols={2} gap="md">
                {expert.experiences.map(exp => (
                  <ExperienceCard key={exp.id} experience={exp} />
                ))}
              </Grid>
            </section>
          </div>

          {/* Contact Sidebar */}
          <aside className="hidden lg:block w-80 shrink-0">
            <div className="sticky top-24 bg-white rounded-xl border border-gray-200 p-6">
              <div className="text-center mb-6">
                <p className="text-caption text-gray-400">Hourly Rate</p>
                <p className="font-heading text-h2 text-gray-900">
                  RM {expert.hourlyRate}
                </p>
              </div>
              <Button variant="solid" className="w-full mb-3">
                Book a Session
              </Button>
              <Button variant="outline" className="w-full">
                Send Message
              </Button>
            </div>
          </aside>
        </div>
      </Container>
    </DetailLayout>
  )
}
```

---

## 2. App Pages (app.mereka.io)

Authenticated application interface.

### Dashboard Layout with Sidebar

```tsx
interface AppLayoutProps {
  children: React.ReactNode
  sidebar?: React.ReactNode
}

export function AppLayout({ children, sidebar }: AppLayoutProps) {
  const [sidebarOpen, setSidebarOpen] = useState(false)

  return (
    <div className="min-h-screen bg-gray-50">
      {/* App Header */}
      <header className="sticky top-0 z-30 bg-white border-b border-gray-200">
        <Container size="full" className="h-16 flex items-center justify-between">
          {/* Mobile Menu */}
          <Button
            variant="ghost"
            size="icon"
            className="lg:hidden"
            onClick={() => setSidebarOpen(true)}
          >
            <MenuIcon />
          </Button>

          {/* Logo */}
          <a href="/dashboard">
            <MerekaLogo className="h-8" />
          </a>

          {/* Search (Desktop) */}
          <div className="hidden md:block flex-1 max-w-xl mx-8">
            <SearchBar placeholder="Search..." />
          </div>

          {/* User Menu */}
          <div className="flex items-center gap-4">
            <Button variant="ghost" size="icon" aria-label="Notifications">
              <BellIcon />
              <NotificationBadge count={3} />
            </Button>

            <UserDropdown user={user} />
          </div>
        </Container>
      </header>

      <div className="flex">
        {/* Sidebar - Desktop */}
        <aside className="hidden lg:flex lg:flex-col lg:w-64 lg:fixed lg:inset-y-0 lg:top-16 lg:border-r lg:border-gray-200 lg:bg-white">
          <nav className="flex-1 px-4 py-6 space-y-1 overflow-y-auto">
            {sidebar || <DefaultSidebar />}
          </nav>
        </aside>

        {/* Sidebar - Mobile Drawer */}
        <Drawer open={sidebarOpen} onOpenChange={setSidebarOpen}>
          <Drawer.Content side="left" className="w-64">
            <nav className="px-4 py-6 space-y-1">
              {sidebar || <DefaultSidebar />}
            </nav>
          </Drawer.Content>
        </Drawer>

        {/* Main Content */}
        <main className="flex-1 lg:pl-64">
          <Container size="full" className="py-8">
            {children}
          </Container>
        </main>
      </div>
    </div>
  )
}
```

#### Default Sidebar Navigation

```tsx
function DefaultSidebar() {
  const navItems = [
    { label: 'Dashboard', href: '/dashboard', icon: HomeIcon },
    { label: 'My Bookings', href: '/bookings', icon: CalendarIcon },
    { label: 'Saved', href: '/saved', icon: HeartIcon },
    { label: 'Messages', href: '/messages', icon: MailIcon, badge: 2 },
    { label: 'Profile', href: '/profile', icon: UserIcon },
    { label: 'Settings', href: '/settings', icon: SettingsIcon },
  ]

  return (
    <>
      {navItems.map(item => (
        <NavLink
          key={item.href}
          href={item.href}
          className={({ isActive }) =>
            `flex items-center gap-3 px-3 py-2 rounded-lg text-body transition-colors ${
              isActive
                ? 'bg-gray-100 text-gray-900 font-medium'
                : 'text-gray-600 hover:bg-gray-50 hover:text-gray-900'
            }`
          }
        >
          <item.icon className="w-5 h-5" />
          <span className="flex-1">{item.label}</span>
          {item.badge && (
            <span className="w-5 h-5 bg-mereka-teal text-white text-xs rounded-full flex items-center justify-center">
              {item.badge}
            </span>
          )}
        </NavLink>
      ))}
    </>
  )
}
```

#### Dashboard Page

```tsx
export function DashboardPage() {
  return (
    <AppLayout>
      {/* Page Header */}
      <div className="mb-8">
        <h1 className="font-heading text-h1 text-gray-900">Welcome back, {user.name}</h1>
        <p className="text-body text-gray-600 mt-1">
          Here's what's happening with your learning journey.
        </p>
      </div>

      {/* Stats Cards */}
      <Grid cols={4} gap="md" className="mb-8">
        <StatCard
          label="Upcoming"
          value={3}
          icon={CalendarIcon}
          trend={{ value: 2, direction: 'up' }}
        />
        <StatCard
          label="Completed"
          value={12}
          icon={CheckCircleIcon}
        />
        <StatCard
          label="Hours Learned"
          value={48}
          icon={ClockIcon}
        />
        <StatCard
          label="Reviews"
          value={8}
          icon={StarIcon}
        />
      </Grid>

      {/* Two Column Layout */}
      <div className="grid lg:grid-cols-3 gap-8">
        {/* Main Content - 2 cols */}
        <div className="lg:col-span-2 space-y-8">
          {/* Upcoming Bookings */}
          <section>
            <div className="flex items-center justify-between mb-4">
              <h2 className="font-heading text-h2 text-gray-900">Upcoming Bookings</h2>
              <Button variant="ghost" size="small">View all</Button>
            </div>
            <div className="space-y-4">
              {upcomingBookings.map(booking => (
                <BookingCard key={booking.id} booking={booking} />
              ))}
            </div>
          </section>

          {/* Recommended */}
          <section>
            <div className="flex items-center justify-between mb-4">
              <h2 className="font-heading text-h2 text-gray-900">Recommended for You</h2>
              <Button variant="ghost" size="small">Browse all</Button>
            </div>
            <Grid cols={2} gap="md">
              {recommended.map(exp => (
                <ExperienceCard key={exp.id} experience={exp} />
              ))}
            </Grid>
          </section>
        </div>

        {/* Sidebar - 1 col */}
        <div className="space-y-6">
          {/* Activity Feed */}
          <section className="bg-white rounded-xl border border-gray-200 p-6">
            <h3 className="font-heading text-h4 text-gray-900 mb-4">Recent Activity</h3>
            <div className="space-y-4">
              {activities.map(activity => (
                <ActivityItem key={activity.id} activity={activity} />
              ))}
            </div>
          </section>

          {/* Quick Actions */}
          <section className="bg-white rounded-xl border border-gray-200 p-6">
            <h3 className="font-heading text-h4 text-gray-900 mb-4">Quick Actions</h3>
            <div className="space-y-2">
              <Button variant="outline" className="w-full justify-start">
                <PlusIcon className="w-4 h-4 mr-2" />
                Book an experience
              </Button>
              <Button variant="outline" className="w-full justify-start">
                <SearchIcon className="w-4 h-4 mr-2" />
                Find an expert
              </Button>
            </div>
          </section>
        </div>
      </div>
    </AppLayout>
  )
}
```

### Settings Layout with Tabs

```tsx
export function SettingsLayout({ children }: { children: React.ReactNode }) {
  return (
    <AppLayout>
      <div className="max-w-4xl">
        {/* Page Header */}
        <div className="mb-8">
          <h1 className="font-heading text-h1 text-gray-900">Settings</h1>
          <p className="text-body text-gray-600 mt-1">
            Manage your account preferences and settings.
          </p>
        </div>

        {/* Tabs Navigation */}
        <Tabs defaultValue="profile" className="space-y-8">
          <Tabs.List className="border-b border-gray-200">
            <Tabs.Trigger value="profile">Profile</Tabs.Trigger>
            <Tabs.Trigger value="account">Account</Tabs.Trigger>
            <Tabs.Trigger value="notifications">Notifications</Tabs.Trigger>
            <Tabs.Trigger value="privacy">Privacy</Tabs.Trigger>
            <Tabs.Trigger value="billing">Billing</Tabs.Trigger>
          </Tabs.List>

          {children}
        </Tabs>
      </div>
    </AppLayout>
  )
}

export function ProfileSettingsPage() {
  return (
    <SettingsLayout>
      <Tabs.Content value="profile">
        <div className="bg-white rounded-xl border border-gray-200 p-6">
          <h2 className="font-heading text-h3 text-gray-900 mb-6">Profile Information</h2>

          <form className="space-y-6">
            {/* Avatar */}
            <div className="flex items-center gap-6">
              <Avatar src={user.avatar} alt={user.name} size="xl" />
              <div>
                <Button variant="outline" size="small">Change Photo</Button>
                <p className="text-caption text-gray-400 mt-1">
                  JPG or PNG, max 2MB
                </p>
              </div>
            </div>

            {/* Form Fields */}
            <div className="grid md:grid-cols-2 gap-6">
              <Input label="First Name" defaultValue={user.firstName} />
              <Input label="Last Name" defaultValue={user.lastName} />
            </div>

            <Input label="Email" type="email" defaultValue={user.email} />

            <Input label="Bio" as="textarea" rows={4} defaultValue={user.bio} />

            <Input label="Location" defaultValue={user.location} />

            {/* Submit */}
            <div className="flex justify-end gap-3 pt-6 border-t border-gray-200">
              <Button variant="ghost">Cancel</Button>
              <Button variant="solid">Save Changes</Button>
            </div>
          </form>
        </div>
      </Tabs.Content>

      <Tabs.Content value="notifications">
        <div className="bg-white rounded-xl border border-gray-200 p-6">
          <h2 className="font-heading text-h3 text-gray-900 mb-6">Notification Preferences</h2>

          <div className="space-y-6">
            {/* Email Notifications */}
            <div>
              <h3 className="font-heading text-h5 text-gray-900 mb-4">Email Notifications</h3>
              <div className="space-y-4">
                <Toggle
                  label="Booking confirmations"
                  description="Receive email when a booking is confirmed"
                  defaultChecked
                />
                <Toggle
                  label="Booking reminders"
                  description="Get reminded before your scheduled experiences"
                  defaultChecked
                />
                <Toggle
                  label="New messages"
                  description="Receive email for new messages from hosts"
                  defaultChecked
                />
                <Toggle
                  label="Marketing emails"
                  description="Receive news and promotional offers"
                />
              </div>
            </div>

            {/* Push Notifications */}
            <div className="pt-6 border-t border-gray-200">
              <h3 className="font-heading text-h5 text-gray-900 mb-4">Push Notifications</h3>
              <div className="space-y-4">
                <Toggle
                  label="Enable push notifications"
                  description="Receive notifications on your device"
                  defaultChecked
                />
              </div>
            </div>
          </div>
        </div>
      </Tabs.Content>
    </SettingsLayout>
  )
}
```

### Profile Layout

```tsx
export function ProfilePage() {
  return (
    <AppLayout>
      <div className="max-w-4xl">
        {/* Profile Header */}
        <div className="bg-white rounded-xl border border-gray-200 p-6 mb-8">
          <div className="flex flex-col md:flex-row gap-6">
            <Avatar src={user.avatar} alt={user.name} size="2xl" />
            <div className="flex-1">
              <div className="flex items-start justify-between">
                <div>
                  <h1 className="font-heading text-h1 text-gray-900">{user.name}</h1>
                  <p className="text-body text-gray-600 mt-1">{user.location}</p>
                </div>
                <Button variant="outline" size="small">
                  <EditIcon className="w-4 h-4 mr-2" />
                  Edit Profile
                </Button>
              </div>
              <p className="text-body text-gray-600 mt-4">{user.bio}</p>
              <div className="flex gap-6 mt-4 text-body-sm text-gray-400">
                <span>{user.completedCount} experiences completed</span>
                <span>Member since {formatDate(user.createdAt)}</span>
              </div>
            </div>
          </div>
        </div>

        {/* Content Tabs */}
        <Tabs defaultValue="bookings">
          <Tabs.List>
            <Tabs.Trigger value="bookings">My Bookings</Tabs.Trigger>
            <Tabs.Trigger value="saved">Saved</Tabs.Trigger>
            <Tabs.Trigger value="reviews">My Reviews</Tabs.Trigger>
          </Tabs.List>

          <Tabs.Content value="bookings" className="mt-6">
            <div className="space-y-4">
              {bookings.map(booking => (
                <BookingCard key={booking.id} booking={booking} />
              ))}
            </div>
          </Tabs.Content>

          <Tabs.Content value="saved" className="mt-6">
            <Grid cols={3} gap="md">
              {savedExperiences.map(exp => (
                <ExperienceCard key={exp.id} experience={exp} />
              ))}
            </Grid>
          </Tabs.Content>

          <Tabs.Content value="reviews" className="mt-6">
            <div className="space-y-6">
              {reviews.map(review => (
                <ReviewCard key={review.id} review={review} showExperience />
              ))}
            </div>
          </Tabs.Content>
        </Tabs>
      </div>
    </AppLayout>
  )
}
```

---

## 3. Checkout Pages (checkout.mereka.io)

Focused, distraction-free checkout experience.

### Checkout Layout

Single column with order summary sidebar.

```tsx
export function CheckoutLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="min-h-screen bg-gray-50 flex flex-col">
      {/* Minimal Header */}
      <header className="bg-white border-b border-gray-200">
        <Container size="standard" className="h-16 flex items-center justify-between">
          <a href="/">
            <MerekaLogo className="h-8" />
          </a>
          <div className="flex items-center gap-2 text-body-sm text-gray-600">
            <LockIcon className="w-4 h-4" />
            <span>Secure Checkout</span>
          </div>
        </Container>
      </header>

      {/* Main Content */}
      <main className="flex-1 py-8 md:py-12">
        <Container size="standard">
          {children}
        </Container>
      </main>

      {/* Minimal Footer */}
      <footer className="bg-white border-t border-gray-200 py-6">
        <Container size="standard">
          <div className="flex items-center justify-between text-caption text-gray-400">
            <div className="flex items-center gap-4">
              <a href="/help" className="hover:text-gray-600">Help</a>
              <a href="/terms" className="hover:text-gray-600">Terms</a>
              <a href="/privacy" className="hover:text-gray-600">Privacy</a>
            </div>
            <span>2024 Mereka</span>
          </div>
        </Container>
      </footer>
    </div>
  )
}
```

### Checkout Page

```tsx
export function CheckoutPage({ booking }: { booking: BookingDraft }) {
  return (
    <CheckoutLayout>
      <div className="lg:flex lg:gap-12">
        {/* Main Form - Left */}
        <div className="flex-1 min-w-0">
          <h1 className="font-heading text-h1 text-gray-900 mb-8">Complete your booking</h1>

          {/* Progress Steps */}
          <div className="flex items-center gap-2 mb-8">
            {['Details', 'Payment', 'Confirmation'].map((step, index) => (
              <React.Fragment key={step}>
                <div
                  className={`
                    flex items-center gap-2 px-3 py-1 rounded-full text-body-sm
                    ${index === currentStep ? 'bg-gray-900 text-white' : ''}
                    ${index < currentStep ? 'text-mereka-teal' : 'text-gray-400'}
                  `}
                >
                  {index < currentStep ? (
                    <CheckIcon className="w-4 h-4" />
                  ) : (
                    <span>{index + 1}</span>
                  )}
                  <span>{step}</span>
                </div>
                {index < 2 && (
                  <div className={`flex-1 h-px ${index < currentStep ? 'bg-mereka-teal' : 'bg-gray-200'}`} />
                )}
              </React.Fragment>
            ))}
          </div>

          {/* Step Content */}
          <div className="bg-white rounded-xl border border-gray-200 p-6">
            {currentStep === 0 && (
              <div className="space-y-6">
                <h2 className="font-heading text-h3 text-gray-900">Your Details</h2>

                <div className="grid md:grid-cols-2 gap-4">
                  <Input label="First Name" required />
                  <Input label="Last Name" required />
                </div>

                <Input label="Email" type="email" required />

                <Input label="Phone" type="tel" />

                <Input
                  label="Special Requests"
                  as="textarea"
                  rows={3}
                  placeholder="Any dietary requirements, accessibility needs, etc."
                />
              </div>
            )}

            {currentStep === 1 && (
              <div className="space-y-6">
                <h2 className="font-heading text-h3 text-gray-900">Payment</h2>

                <div className="space-y-4">
                  <Radio name="payment" value="card" label="Credit/Debit Card" defaultChecked />
                  <Radio name="payment" value="fpx" label="FPX Online Banking" />
                  <Radio name="payment" value="grabpay" label="GrabPay" />
                </div>

                {/* Card Form */}
                <div className="pt-6 border-t border-gray-200 space-y-4">
                  <Input label="Card Number" placeholder="1234 5678 9012 3456" />
                  <div className="grid grid-cols-2 gap-4">
                    <Input label="Expiry Date" placeholder="MM/YY" />
                    <Input label="CVC" placeholder="123" />
                  </div>
                  <Input label="Cardholder Name" />
                </div>
              </div>
            )}

            {currentStep === 2 && (
              <div className="text-center py-8">
                <div className="w-16 h-16 bg-green-100 rounded-full flex items-center justify-center mx-auto mb-4">
                  <CheckIcon className="w-8 h-8 text-green-600" />
                </div>
                <h2 className="font-heading text-h2 text-gray-900">Booking Confirmed!</h2>
                <p className="text-body text-gray-600 mt-2">
                  We've sent a confirmation email to {email}
                </p>
                <Button variant="solid" className="mt-6">
                  View Booking Details
                </Button>
              </div>
            )}
          </div>

          {/* Navigation */}
          {currentStep < 2 && (
            <div className="flex justify-between mt-6">
              <Button
                variant="ghost"
                onClick={handleBack}
                disabled={currentStep === 0}
              >
                <ArrowLeftIcon className="w-4 h-4 mr-2" />
                Back
              </Button>
              <Button variant="solid" onClick={handleNext}>
                {currentStep === 1 ? 'Pay Now' : 'Continue'}
                <ArrowRightIcon className="w-4 h-4 ml-2" />
              </Button>
            </div>
          )}
        </div>

        {/* Order Summary Sidebar - Right */}
        <aside className="hidden lg:block w-96 shrink-0">
          <div className="sticky top-8 bg-white rounded-xl border border-gray-200 p-6">
            <h2 className="font-heading text-h4 text-gray-900 mb-4">Order Summary</h2>

            {/* Experience Preview */}
            <div className="flex gap-4 pb-6 border-b border-gray-200">
              <img
                src={booking.experience.coverImage}
                alt={booking.experience.title}
                className="w-20 h-20 rounded-lg object-cover"
              />
              <div>
                <h3 className="font-heading text-h5 text-gray-900 line-clamp-2">
                  {booking.experience.title}
                </h3>
                <p className="text-body-sm text-gray-600 mt-1">
                  {booking.experience.host.name}
                </p>
              </div>
            </div>

            {/* Booking Details */}
            <div className="py-6 border-b border-gray-200 space-y-3">
              <div className="flex items-center gap-2 text-body">
                <CalendarIcon className="w-5 h-5 text-gray-400" />
                <span>{formatDate(booking.date)}</span>
              </div>
              <div className="flex items-center gap-2 text-body">
                <ClockIcon className="w-5 h-5 text-gray-400" />
                <span>{formatTime(booking.time)}</span>
              </div>
              <div className="flex items-center gap-2 text-body">
                <UsersIcon className="w-5 h-5 text-gray-400" />
                <span>{booking.guests} guest{booking.guests > 1 ? 's' : ''}</span>
              </div>
            </div>

            {/* Price Breakdown */}
            <div className="py-6 space-y-3">
              <div className="flex justify-between text-body">
                <span>RM {booking.price} x {booking.guests}</span>
                <span>RM {booking.price * booking.guests}</span>
              </div>
              <div className="flex justify-between text-body">
                <span>Service fee</span>
                <span>RM {booking.serviceFee}</span>
              </div>
            </div>

            {/* Total */}
            <div className="pt-6 border-t border-gray-200">
              <div className="flex justify-between font-heading text-h3">
                <span>Total</span>
                <span>RM {booking.total}</span>
              </div>
            </div>
          </div>
        </aside>
      </div>

      {/* Mobile Order Summary (Collapsible) */}
      <div className="lg:hidden mt-8">
        <Collapsible>
          <Collapsible.Trigger className="flex items-center justify-between w-full bg-white rounded-xl border border-gray-200 p-4">
            <span className="font-heading text-h4">Order Summary</span>
            <span className="font-heading text-h4">RM {booking.total}</span>
          </Collapsible.Trigger>
          <Collapsible.Content className="bg-white border border-t-0 border-gray-200 rounded-b-xl p-4">
            {/* Same content as sidebar */}
          </Collapsible.Content>
        </Collapsible>
      </div>
    </CheckoutLayout>
  )
}
```

---

## 4. Admin Pages (admin.mereka.io)

Internal administration interface.

### Admin Layout

```tsx
export function AdminLayout({ children }: { children: React.ReactNode }) {
  const [sidebarCollapsed, setSidebarCollapsed] = useState(false)

  return (
    <div className="min-h-screen bg-gray-100">
      {/* Admin Header */}
      <header className="fixed top-0 left-0 right-0 z-40 h-16 bg-gray-900 text-white">
        <div className="h-full px-4 flex items-center justify-between">
          <div className="flex items-center gap-4">
            <Button
              variant="ghost"
              size="icon"
              onClick={() => setSidebarCollapsed(!sidebarCollapsed)}
              className="text-white hover:bg-gray-800"
            >
              <MenuIcon />
            </Button>
            <a href="/admin" className="flex items-center gap-2">
              <MerekaLogo className="h-6 text-white" />
              <span className="font-heading text-h5">Admin</span>
            </a>
          </div>

          <div className="flex items-center gap-4">
            <div className="hidden md:block w-64">
              <SearchBar
                placeholder="Search..."
                className="bg-gray-800 border-gray-700 text-white"
              />
            </div>
            <UserDropdown user={adminUser} />
          </div>
        </div>
      </header>

      {/* Sidebar */}
      <aside
        className={`
          fixed top-16 bottom-0 left-0 z-30 bg-white border-r border-gray-200
          transition-all duration-200
          ${sidebarCollapsed ? 'w-16' : 'w-64'}
        `}
      >
        <nav className="py-4">
          <AdminNavigation collapsed={sidebarCollapsed} />
        </nav>
      </aside>

      {/* Main Content */}
      <main
        className={`
          pt-16 transition-all duration-200
          ${sidebarCollapsed ? 'pl-16' : 'pl-64'}
        `}
      >
        <div className="p-6 md:p-8">
          {children}
        </div>
      </main>
    </div>
  )
}
```

#### Admin Navigation

```tsx
function AdminNavigation({ collapsed }: { collapsed: boolean }) {
  const sections = [
    {
      title: 'Overview',
      items: [
        { label: 'Dashboard', href: '/admin', icon: LayoutDashboardIcon },
        { label: 'Analytics', href: '/admin/analytics', icon: BarChartIcon },
      ],
    },
    {
      title: 'Content',
      items: [
        { label: 'Experiences', href: '/admin/experiences', icon: CalendarIcon },
        { label: 'Hubs', href: '/admin/hubs', icon: BuildingIcon },
        { label: 'Experts', href: '/admin/experts', icon: UsersIcon },
        { label: 'Categories', href: '/admin/categories', icon: TagIcon },
      ],
    },
    {
      title: 'Users',
      items: [
        { label: 'All Users', href: '/admin/users', icon: UserIcon },
        { label: 'Roles', href: '/admin/roles', icon: ShieldIcon },
      ],
    },
    {
      title: 'Finance',
      items: [
        { label: 'Bookings', href: '/admin/bookings', icon: TicketIcon },
        { label: 'Payments', href: '/admin/payments', icon: CreditCardIcon },
        { label: 'Payouts', href: '/admin/payouts', icon: BanknoteIcon },
      ],
    },
    {
      title: 'Settings',
      items: [
        { label: 'Settings', href: '/admin/settings', icon: SettingsIcon },
      ],
    },
  ]

  return (
    <div className="space-y-6">
      {sections.map(section => (
        <div key={section.title}>
          {!collapsed && (
            <p className="px-4 mb-2 text-overline text-gray-400 uppercase">
              {section.title}
            </p>
          )}
          <div className="space-y-1">
            {section.items.map(item => (
              <NavLink
                key={item.href}
                href={item.href}
                className={({ isActive }) =>
                  `flex items-center gap-3 px-4 py-2 text-body transition-colors ${
                    isActive
                      ? 'bg-gray-100 text-gray-900 font-medium border-r-2 border-mereka-teal'
                      : 'text-gray-600 hover:bg-gray-50 hover:text-gray-900'
                  } ${collapsed ? 'justify-center' : ''}`
                }
                title={collapsed ? item.label : undefined}
              >
                <item.icon className="w-5 h-5 shrink-0" />
                {!collapsed && <span>{item.label}</span>}
              </NavLink>
            ))}
          </div>
        </div>
      ))}
    </div>
  )
}
```

### Admin Dashboard Page

```tsx
export function AdminDashboardPage() {
  return (
    <AdminLayout>
      {/* Page Header */}
      <div className="flex items-center justify-between mb-8">
        <div>
          <h1 className="font-heading text-h1 text-gray-900">Dashboard</h1>
          <p className="text-body text-gray-600 mt-1">
            Overview of your platform performance
          </p>
        </div>
        <div className="flex items-center gap-3">
          <Select defaultValue="7d">
            <Select.Option value="24h">Last 24 hours</Select.Option>
            <Select.Option value="7d">Last 7 days</Select.Option>
            <Select.Option value="30d">Last 30 days</Select.Option>
            <Select.Option value="90d">Last 90 days</Select.Option>
          </Select>
          <Button variant="outline">
            <DownloadIcon className="w-4 h-4 mr-2" />
            Export
          </Button>
        </div>
      </div>

      {/* Stats Grid */}
      <Grid cols={4} gap="md" className="mb-8">
        <StatCard
          label="Total Revenue"
          value="RM 125,430"
          change={{ value: 12.5, direction: 'up' }}
          icon={BanknoteIcon}
        />
        <StatCard
          label="Total Bookings"
          value="1,234"
          change={{ value: 8.2, direction: 'up' }}
          icon={TicketIcon}
        />
        <StatCard
          label="Active Users"
          value="8,901"
          change={{ value: 3.1, direction: 'up' }}
          icon={UsersIcon}
        />
        <StatCard
          label="Active Hubs"
          value="156"
          change={{ value: -2.4, direction: 'down' }}
          icon={BuildingIcon}
        />
      </Grid>

      {/* Charts Row */}
      <div className="grid lg:grid-cols-2 gap-6 mb-8">
        <div className="bg-white rounded-xl border border-gray-200 p-6">
          <h2 className="font-heading text-h4 text-gray-900 mb-4">Revenue Trend</h2>
          <RevenueChart data={revenueData} />
        </div>
        <div className="bg-white rounded-xl border border-gray-200 p-6">
          <h2 className="font-heading text-h4 text-gray-900 mb-4">Bookings Trend</h2>
          <BookingsChart data={bookingsData} />
        </div>
      </div>

      {/* Tables Row */}
      <div className="grid lg:grid-cols-2 gap-6">
        {/* Recent Bookings */}
        <div className="bg-white rounded-xl border border-gray-200 p-6">
          <div className="flex items-center justify-between mb-4">
            <h2 className="font-heading text-h4 text-gray-900">Recent Bookings</h2>
            <Button variant="ghost" size="small">View all</Button>
          </div>
          <table className="w-full">
            <thead>
              <tr className="text-left text-caption text-gray-400 border-b border-gray-200">
                <th className="pb-3 font-medium">Booking</th>
                <th className="pb-3 font-medium">Status</th>
                <th className="pb-3 font-medium text-right">Amount</th>
              </tr>
            </thead>
            <tbody>
              {recentBookings.map(booking => (
                <tr key={booking.id} className="border-b border-gray-100 last:border-0">
                  <td className="py-3">
                    <p className="font-medium text-gray-900">{booking.experience}</p>
                    <p className="text-caption text-gray-400">{booking.user}</p>
                  </td>
                  <td className="py-3">
                    <BookingStatusBadge status={booking.status} />
                  </td>
                  <td className="py-3 text-right font-medium">
                    RM {booking.amount}
                  </td>
                </tr>
              ))}
            </tbody>
          </table>
        </div>

        {/* Top Experiences */}
        <div className="bg-white rounded-xl border border-gray-200 p-6">
          <div className="flex items-center justify-between mb-4">
            <h2 className="font-heading text-h4 text-gray-900">Top Experiences</h2>
            <Button variant="ghost" size="small">View all</Button>
          </div>
          <div className="space-y-4">
            {topExperiences.map((exp, index) => (
              <div key={exp.id} className="flex items-center gap-4">
                <span className="w-6 h-6 rounded-full bg-gray-100 flex items-center justify-center text-caption font-medium">
                  {index + 1}
                </span>
                <img
                  src={exp.coverImage}
                  alt={exp.title}
                  className="w-12 h-12 rounded-lg object-cover"
                />
                <div className="flex-1 min-w-0">
                  <p className="font-medium text-gray-900 truncate">{exp.title}</p>
                  <p className="text-caption text-gray-400">{exp.bookings} bookings</p>
                </div>
                <p className="font-medium text-gray-900">RM {exp.revenue}</p>
              </div>
            ))}
          </div>
        </div>
      </div>
    </AdminLayout>
  )
}
```

### Data Table Page

```tsx
export function AdminExperiencesPage() {
  return (
    <AdminLayout>
      {/* Page Header */}
      <div className="flex items-center justify-between mb-8">
        <div>
          <h1 className="font-heading text-h1 text-gray-900">Experiences</h1>
          <p className="text-body text-gray-600 mt-1">
            {totalCount} experiences total
          </p>
        </div>
        <Button variant="solid">
          <PlusIcon className="w-4 h-4 mr-2" />
          Add Experience
        </Button>
      </div>

      {/* Filters Bar */}
      <div className="bg-white rounded-xl border border-gray-200 p-4 mb-6">
        <div className="flex flex-wrap items-center gap-4">
          <div className="flex-1 min-w-64">
            <SearchBar placeholder="Search experiences..." />
          </div>
          <Select placeholder="Status">
            <Select.Option value="all">All Status</Select.Option>
            <Select.Option value="active">Active</Select.Option>
            <Select.Option value="draft">Draft</Select.Option>
            <Select.Option value="archived">Archived</Select.Option>
          </Select>
          <Select placeholder="Category">
            <Select.Option value="all">All Categories</Select.Option>
            <Select.Option value="workshop">Workshop</Select.Option>
            <Select.Option value="tour">Tour</Select.Option>
            <Select.Option value="class">Class</Select.Option>
          </Select>
          <Select placeholder="Hub">
            <Select.Option value="all">All Hubs</Select.Option>
            {hubs.map(hub => (
              <Select.Option key={hub.id} value={hub.id}>{hub.name}</Select.Option>
            ))}
          </Select>
          <Button variant="ghost">
            <FilterIcon className="w-4 h-4 mr-2" />
            More Filters
          </Button>
        </div>
      </div>

      {/* Data Table */}
      <div className="bg-white rounded-xl border border-gray-200 overflow-hidden">
        <table className="w-full">
          <thead className="bg-gray-50 border-b border-gray-200">
            <tr className="text-left text-caption font-medium text-gray-500">
              <th className="px-6 py-4">
                <Checkbox />
              </th>
              <th className="px-6 py-4">Experience</th>
              <th className="px-6 py-4">Hub</th>
              <th className="px-6 py-4">Category</th>
              <th className="px-6 py-4">Price</th>
              <th className="px-6 py-4">Bookings</th>
              <th className="px-6 py-4">Status</th>
              <th className="px-6 py-4">Actions</th>
            </tr>
          </thead>
          <tbody className="divide-y divide-gray-100">
            {experiences.map(exp => (
              <tr key={exp.id} className="hover:bg-gray-50">
                <td className="px-6 py-4">
                  <Checkbox />
                </td>
                <td className="px-6 py-4">
                  <div className="flex items-center gap-3">
                    <img
                      src={exp.coverImage}
                      alt={exp.title}
                      className="w-10 h-10 rounded-lg object-cover"
                    />
                    <div>
                      <p className="font-medium text-gray-900">{exp.title}</p>
                      <p className="text-caption text-gray-400">
                        {formatDate(exp.createdAt)}
                      </p>
                    </div>
                  </div>
                </td>
                <td className="px-6 py-4">
                  <div className="flex items-center gap-2">
                    <Avatar src={exp.hub.logo} alt={exp.hub.name} size="xs" />
                    <span className="text-body">{exp.hub.name}</span>
                  </div>
                </td>
                <td className="px-6 py-4">
                  <Tag variant="standard">{exp.category}</Tag>
                </td>
                <td className="px-6 py-4 font-medium">
                  RM {exp.price}
                </td>
                <td className="px-6 py-4">
                  {exp.bookingCount}
                </td>
                <td className="px-6 py-4">
                  <Tag variant={exp.status === 'active' ? 'positive' : 'neutral'}>
                    {exp.status}
                  </Tag>
                </td>
                <td className="px-6 py-4">
                  <DropdownMenu>
                    <DropdownMenu.Trigger asChild>
                      <Button variant="ghost" size="icon">
                        <MoreHorizontalIcon />
                      </Button>
                    </DropdownMenu.Trigger>
                    <DropdownMenu.Content align="end">
                      <DropdownMenu.Item>
                        <EyeIcon className="w-4 h-4 mr-2" />
                        View
                      </DropdownMenu.Item>
                      <DropdownMenu.Item>
                        <EditIcon className="w-4 h-4 mr-2" />
                        Edit
                      </DropdownMenu.Item>
                      <DropdownMenu.Item>
                        <CopyIcon className="w-4 h-4 mr-2" />
                        Duplicate
                      </DropdownMenu.Item>
                      <DropdownMenu.Separator />
                      <DropdownMenu.Item className="text-red-600">
                        <TrashIcon className="w-4 h-4 mr-2" />
                        Delete
                      </DropdownMenu.Item>
                    </DropdownMenu.Content>
                  </DropdownMenu>
                </td>
              </tr>
            ))}
          </tbody>
        </table>

        {/* Table Footer */}
        <div className="px-6 py-4 border-t border-gray-200 flex items-center justify-between">
          <p className="text-body-sm text-gray-600">
            Showing {startIndex + 1} to {endIndex} of {totalCount} results
          </p>
          <Pagination
            currentPage={page}
            totalPages={totalPages}
            onPageChange={setPage}
          />
        </div>
      </div>
    </AdminLayout>
  )
}
```

### Detail/Edit Page

```tsx
export function AdminExperienceEditPage({ experience }: { experience: Experience }) {
  return (
    <AdminLayout>
      {/* Page Header */}
      <div className="flex items-center justify-between mb-8">
        <div className="flex items-center gap-4">
          <Button variant="ghost" size="icon" asChild>
            <a href="/admin/experiences">
              <ArrowLeftIcon />
            </a>
          </Button>
          <div>
            <h1 className="font-heading text-h1 text-gray-900">Edit Experience</h1>
            <p className="text-body text-gray-600 mt-1">{experience.title}</p>
          </div>
        </div>
        <div className="flex items-center gap-3">
          <Button variant="ghost">Preview</Button>
          <Button variant="outline">Save Draft</Button>
          <Button variant="solid">Publish</Button>
        </div>
      </div>

      {/* Form Layout */}
      <div className="grid lg:grid-cols-3 gap-8">
        {/* Main Form - 2 cols */}
        <div className="lg:col-span-2 space-y-6">
          {/* Basic Info */}
          <div className="bg-white rounded-xl border border-gray-200 p-6">
            <h2 className="font-heading text-h4 text-gray-900 mb-6">Basic Information</h2>
            <div className="space-y-4">
              <Input
                label="Title"
                defaultValue={experience.title}
                required
              />
              <Input
                label="Description"
                as="textarea"
                rows={4}
                defaultValue={experience.description}
                required
              />
              <div className="grid md:grid-cols-2 gap-4">
                <Select label="Category" defaultValue={experience.category}>
                  <Select.Option value="workshop">Workshop</Select.Option>
                  <Select.Option value="tour">Tour</Select.Option>
                  <Select.Option value="class">Class</Select.Option>
                </Select>
                <Input
                  label="Duration"
                  defaultValue={experience.duration}
                  placeholder="e.g., 2 hours"
                />
              </div>
            </div>
          </div>

          {/* Images */}
          <div className="bg-white rounded-xl border border-gray-200 p-6">
            <h2 className="font-heading text-h4 text-gray-900 mb-6">Images</h2>
            <ImageUploader
              images={experience.images}
              onUpload={handleUpload}
              onRemove={handleRemove}
            />
          </div>

          {/* Pricing */}
          <div className="bg-white rounded-xl border border-gray-200 p-6">
            <h2 className="font-heading text-h4 text-gray-900 mb-6">Pricing</h2>
            <div className="grid md:grid-cols-2 gap-4">
              <Input
                label="Price (RM)"
                type="number"
                defaultValue={experience.price}
                required
              />
              <Input
                label="Max Participants"
                type="number"
                defaultValue={experience.maxParticipants}
              />
            </div>
          </div>

          {/* Location */}
          <div className="bg-white rounded-xl border border-gray-200 p-6">
            <h2 className="font-heading text-h4 text-gray-900 mb-6">Location</h2>
            <div className="space-y-4">
              <div className="flex gap-4">
                <Radio name="locationType" value="physical" label="In-Person" />
                <Radio name="locationType" value="virtual" label="Online" />
                <Radio name="locationType" value="hybrid" label="Hybrid" />
              </div>
              <Input
                label="Address"
                defaultValue={experience.location?.address}
              />
            </div>
          </div>
        </div>

        {/* Sidebar - 1 col */}
        <div className="space-y-6">
          {/* Status */}
          <div className="bg-white rounded-xl border border-gray-200 p-6">
            <h2 className="font-heading text-h4 text-gray-900 mb-4">Status</h2>
            <Select defaultValue={experience.status}>
              <Select.Option value="draft">Draft</Select.Option>
              <Select.Option value="active">Active</Select.Option>
              <Select.Option value="archived">Archived</Select.Option>
            </Select>
          </div>

          {/* Hub */}
          <div className="bg-white rounded-xl border border-gray-200 p-6">
            <h2 className="font-heading text-h4 text-gray-900 mb-4">Hub</h2>
            <div className="flex items-center gap-3">
              <Avatar src={experience.hub.logo} alt={experience.hub.name} size="md" />
              <div>
                <p className="font-medium">{experience.hub.name}</p>
                <p className="text-caption text-gray-400">{experience.hub.location}</p>
              </div>
            </div>
          </div>

          {/* Quick Stats */}
          <div className="bg-white rounded-xl border border-gray-200 p-6">
            <h2 className="font-heading text-h4 text-gray-900 mb-4">Stats</h2>
            <div className="space-y-3">
              <div className="flex justify-between">
                <span className="text-gray-600">Total Bookings</span>
                <span className="font-medium">{experience.bookingCount}</span>
              </div>
              <div className="flex justify-between">
                <span className="text-gray-600">Revenue</span>
                <span className="font-medium">RM {experience.revenue}</span>
              </div>
              <div className="flex justify-between">
                <span className="text-gray-600">Rating</span>
                <span className="font-medium flex items-center gap-1">
                  <StarIcon className="w-4 h-4 text-yellow-400 fill-yellow-400" />
                  {experience.rating || 'N/A'}
                </span>
              </div>
            </div>
          </div>

          {/* Danger Zone */}
          <div className="bg-white rounded-xl border border-red-200 p-6">
            <h2 className="font-heading text-h4 text-red-600 mb-4">Danger Zone</h2>
            <Button variant="outline" className="w-full text-red-600 border-red-300 hover:bg-red-50">
              <TrashIcon className="w-4 h-4 mr-2" />
              Delete Experience
            </Button>
          </div>
        </div>
      </div>
    </AdminLayout>
  )
}
```

---

## 5. Common Patterns

### Sticky Headers

```tsx
// Page sticky header
<header className="sticky top-0 z-30 bg-white border-b border-gray-200">
  <Container size="full" className="h-16 flex items-center">
    {/* Header content */}
  </Container>
</header>

// Section sticky header (scrolls with content)
<div className="sticky top-16 z-20 bg-gray-50 py-4 border-b border-gray-200">
  <h2 className="font-heading text-h3">Section Title</h2>
</div>
```

### Sticky Footers

```tsx
// Form action footer
<footer className="sticky bottom-0 z-20 bg-white border-t border-gray-200 py-4">
  <Container size="standard">
    <div className="flex items-center justify-between">
      <Button variant="ghost">Cancel</Button>
      <div className="flex gap-3">
        <Button variant="outline">Save Draft</Button>
        <Button variant="solid">Publish</Button>
      </div>
    </div>
  </Container>
</footer>

// Mobile booking bar
<div className="lg:hidden fixed bottom-0 left-0 right-0 z-30 bg-white border-t border-gray-200 p-4 safe-area-inset-bottom">
  <div className="flex items-center justify-between">
    <div>
      <span className="text-caption text-gray-400">From</span>
      <p className="font-heading text-h3">RM {price}</p>
    </div>
    <Button variant="solid">Book Now</Button>
  </div>
</div>
```

### Sidebar + Main Content Pattern

```tsx
// Desktop sidebar with main content
<div className="flex">
  {/* Fixed Sidebar */}
  <aside className="hidden lg:block w-64 fixed inset-y-0 top-16 border-r border-gray-200 bg-white overflow-y-auto">
    <nav className="p-4">
      {/* Navigation items */}
    </nav>
  </aside>

  {/* Main Content with left padding */}
  <main className="flex-1 lg:pl-64">
    <Container size="full" className="py-8">
      {/* Page content */}
    </Container>
  </main>
</div>

// Sticky sidebar (scrolls but sticks)
<div className="lg:flex lg:gap-8">
  <main className="flex-1 min-w-0">
    {/* Main content */}
  </main>
  <aside className="hidden lg:block w-80 shrink-0">
    <div className="sticky top-24">
      {/* Sidebar content */}
    </div>
  </aside>
</div>
```

### Container Width Decision Matrix

| Page Type | Container | Width | Reason |
|-----------|-----------|-------|--------|
| Landing/Browse | `max-w-7xl` | 1280px | More content, cards, grids |
| Detail/Profile | `max-w-5xl` | 1024px | Focused reading experience |
| Checkout/Auth | `max-w-2xl` | 672px | Single-task focus |
| Modal Forms | `max-w-md` | 448px | Minimal distraction |

### Responsive Grid Patterns

```tsx
// Experience cards
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">

// Hub cards (larger)
<div className="grid grid-cols-1 md:grid-cols-2 gap-8">

// Settings/forms
<div className="grid md:grid-cols-2 gap-4">

// Stats cards
<div className="grid grid-cols-2 md:grid-cols-4 gap-4">

// Content + sidebar
<div className="lg:flex lg:gap-8">
  <div className="flex-1 min-w-0">{/* Main */}</div>
  <div className="hidden lg:block w-80 shrink-0">{/* Sidebar */}</div>
</div>
```

### Full Page Layout Template

```tsx
// Standard page structure
export function PageLayout({
  header,
  children,
  footer,
  sidebar,
  containerSize = 'full',
}: PageLayoutProps) {
  return (
    <div className="min-h-screen flex flex-col bg-gray-50">
      {/* Header */}
      {header}

      {/* Main Content */}
      <div className="flex-1 flex">
        {sidebar && (
          <aside className="hidden lg:block w-64 border-r border-gray-200 bg-white">
            {sidebar}
          </aside>
        )}

        <main className={`flex-1 ${sidebar ? 'lg:pl-0' : ''}`}>
          <Container size={containerSize} className="py-8">
            {children}
          </Container>
        </main>
      </div>

      {/* Footer */}
      {footer}
    </div>
  )
}
```

---

## Quick Reference

### Container Sizes

| Size | Class | Pixels | Use For |
|------|-------|--------|---------|
| Full | `max-w-7xl` | 1280px | Dashboards, browse, admin |
| Standard | `max-w-5xl` | 1024px | Detail pages, forms |
| Narrow | `max-w-2xl` | 672px | Checkout, focused tasks |
| Compact | `max-w-md` | 448px | Modals, auth forms |

### Common Breakpoints

| Breakpoint | Width | Typical Change |
|------------|-------|----------------|
| `sm` | 640px | 1 to 2 columns |
| `md` | 768px | Show desktop nav |
| `lg` | 1024px | Show sidebar |
| `xl` | 1280px | Max columns |

### Z-Index Layers

| Element | Z-Index |
|---------|---------|
| Default | `z-0` |
| Raised elements | `z-10` |
| Dropdowns | `z-20` |
| Sticky header | `z-30` |
| Modal backdrop | `z-40` |
| Modals | `z-50` |
| Tooltips | `z-70` |

### Standard Spacing

| Element | Padding/Gap |
|---------|-------------|
| Page | `py-8 md:py-12` |
| Section | `py-12 md:py-16` |
| Card | `p-6` |
| Grid | `gap-6` |
| Form fields | `space-y-4` |
| Button groups | `gap-3` |
