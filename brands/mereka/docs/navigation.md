# Navigation

> Headers, footers, search, and menus that provide site-wide navigation.

**Figma Section**: Headers/Footers

---

## Header Patterns

### Public Header (Homepage)

For unauthenticated users on landing pages.

```tsx
<header className="sticky top-0 z-30 bg-white border-b border-gray-200">
  <div className="container mx-auto px-4 h-16 flex items-center justify-between">
    {/* Logo */}
    <a href="/">
      <MerekaLogo className="h-8" />
    </a>

    {/* Navigation */}
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

    {/* Actions */}
    <div className="flex items-center gap-3">
      <Button variant="ghost">Log in</Button>
      <Button variant="solid">Sign up</Button>
    </div>
  </div>
</header>
```

### App Header (Authenticated)

For logged-in users in the main application.

```tsx
<header className="sticky top-0 z-30 bg-white border-b border-gray-200">
  <div className="container mx-auto px-4 h-16 flex items-center justify-between">
    {/* Logo */}
    <a href="/dashboard">
      <MerekaLogo className="h-8" />
    </a>

    {/* Search */}
    <div className="flex-1 max-w-xl mx-8">
      <SearchBar placeholder="What are you looking for?" />
    </div>

    {/* User Menu */}
    <div className="flex items-center gap-4">
      <Button variant="ghost" size="icon" aria-label="Notifications">
        <BellIcon />
        <NotificationBadge count={3} />
      </Button>

      <DropdownMenu>
        <DropdownMenu.Trigger>
          <Avatar src={user.avatar} alt={user.name} size="md" />
        </DropdownMenu.Trigger>
        <DropdownMenu.Content>
          <DropdownMenu.Item>Profile</DropdownMenu.Item>
          <DropdownMenu.Item>Settings</DropdownMenu.Item>
          <DropdownMenu.Item>Log out</DropdownMenu.Item>
        </DropdownMenu.Content>
      </DropdownMenu>
    </div>
  </div>
</header>
```

### Header States

| State | Visual Treatment |
|-------|------------------|
| **Zero** | Full navigation, prominent CTAs |
| **Learner** | User avatar, notifications, search |
| **Hub Access** | Hub selector, management links |

---

## Search Bar

The search component is central to the Mereka experience.

### Default State

```tsx
<div className="flex items-center gap-2 bg-white border border-gray-300 rounded-full px-4 py-2 focus-within:border-gray-900 focus-within:ring-2 focus-within:ring-gray-900/10">
  <SearchIcon className="w-5 h-5 text-gray-400" />
  <input
    type="text"
    placeholder="What are you looking for?"
    className="flex-1 bg-transparent outline-none text-body"
  />
  <div className="flex items-center gap-2 border-l border-gray-200 pl-2">
    <CalendarIcon className="w-5 h-5 text-gray-400" />
    <span className="text-body text-gray-400">When?</span>
  </div>
  <Button variant="solid" size="icon" className="ml-2">
    <ArrowRightIcon />
  </Button>
</div>
```

### Search Dropdown

When the search bar is focused:

```tsx
<div className="absolute top-full left-0 right-0 mt-2 bg-white rounded-xl shadow-lg border border-gray-200 p-4">
  {/* View All CTA */}
  <Button variant="solid" className="w-full mb-4">
    View all Experiences
    <ArrowRightIcon className="ml-2" />
  </Button>

  {/* Quick Filters */}
  <div className="space-y-3">
    <button className="flex items-center gap-3 w-full p-2 rounded-lg hover:bg-gray-50">
      <LocationIcon className="w-5 h-5 text-gray-400" />
      <span>Nearby</span>
    </button>
    <button className="flex items-center gap-3 w-full p-2 rounded-lg hover:bg-gray-50">
      <LaptopIcon className="w-5 h-5 text-gray-400" />
      <span>Online Experiences</span>
    </button>
  </div>

  {/* Recently Viewed */}
  <div className="mt-4 pt-4 border-t border-gray-200">
    <span className="text-overline text-gray-400 uppercase">Recently Viewed</span>
    <div className="mt-2">
      {recentItems.map(item => (
        <button className="flex items-center gap-3 w-full p-2 rounded-lg hover:bg-gray-50">
          <HistoryIcon className="w-5 h-5 text-gray-400" />
          <span>{item.name}</span>
        </button>
      ))}
    </div>
  </div>
</div>
```

---

## Footer

### Main Footer

```tsx
<footer className="bg-gray-900 text-white py-16">
  <div className="container mx-auto px-4">
    <div className="grid grid-cols-2 md:grid-cols-4 gap-8">
      {/* Company */}
      <div>
        <h4 className="font-heading text-h6 mb-4">Company</h4>
        <ul className="space-y-2">
          <li><a href="/about" className="text-body-sm text-gray-400 hover:text-white">About</a></li>
          <li><a href="/careers" className="text-body-sm text-gray-400 hover:text-white">Careers</a></li>
          <li><a href="/press" className="text-body-sm text-gray-400 hover:text-white">Press</a></li>
        </ul>
      </div>

      {/* Support */}
      <div>
        <h4 className="font-heading text-h6 mb-4">Support</h4>
        <ul className="space-y-2">
          <li><a href="/help" className="text-body-sm text-gray-400 hover:text-white">Help Center</a></li>
          <li><a href="/contact" className="text-body-sm text-gray-400 hover:text-white">Contact</a></li>
          <li><a href="/faq" className="text-body-sm text-gray-400 hover:text-white">FAQ</a></li>
        </ul>
      </div>

      {/* Legal */}
      <div>
        <h4 className="font-heading text-h6 mb-4">Legal</h4>
        <ul className="space-y-2">
          <li><a href="/privacy" className="text-body-sm text-gray-400 hover:text-white">Privacy</a></li>
          <li><a href="/terms" className="text-body-sm text-gray-400 hover:text-white">Terms</a></li>
        </ul>
      </div>

      {/* Social */}
      <div>
        <h4 className="font-heading text-h6 mb-4">Follow Us</h4>
        <div className="flex gap-4">
          <a href="#" className="text-gray-400 hover:text-white">
            <InstagramIcon className="w-5 h-5" />
          </a>
          <a href="#" className="text-gray-400 hover:text-white">
            <FacebookIcon className="w-5 h-5" />
          </a>
          <a href="#" className="text-gray-400 hover:text-white">
            <LinkedInIcon className="w-5 h-5" />
          </a>
        </div>
      </div>
    </div>

    {/* Bottom */}
    <div className="mt-12 pt-8 border-t border-gray-800 flex items-center justify-between">
      <MerekaLogo className="h-6 text-white" />
      <span className="text-caption text-gray-500">
        © 2024 Mereka. All rights reserved.
      </span>
    </div>
  </div>
</footer>
```

### Form Footer

For multi-step forms and checkout flows:

```tsx
<footer className="sticky bottom-0 bg-white border-t border-gray-200 py-4">
  <div className="container mx-auto px-4 flex items-center justify-between">
    <Button variant="ghost" onClick={onBack}>
      <ArrowLeftIcon className="mr-2" />
      Back
    </Button>
    <Button variant="solid" onClick={onNext}>
      Continue
      <ArrowRightIcon className="ml-2" />
    </Button>
  </div>
</footer>
```

---

## Menus

### User Dropdown Menu

```tsx
<DropdownMenu>
  <DropdownMenu.Content className="w-56">
    {/* User Info */}
    <div className="px-3 py-2 border-b border-gray-200">
      <p className="font-medium">{user.name}</p>
      <p className="text-caption text-gray-400">{user.email}</p>
    </div>

    {/* Links */}
    <DropdownMenu.Item>
      <UserIcon className="mr-2 w-4 h-4" />
      Profile
    </DropdownMenu.Item>
    <DropdownMenu.Item>
      <CalendarIcon className="mr-2 w-4 h-4" />
      My Bookings
    </DropdownMenu.Item>
    <DropdownMenu.Item>
      <HeartIcon className="mr-2 w-4 h-4" />
      Saved
    </DropdownMenu.Item>
    <DropdownMenu.Item>
      <SettingsIcon className="mr-2 w-4 h-4" />
      Settings
    </DropdownMenu.Item>

    <DropdownMenu.Separator />

    <DropdownMenu.Item className="text-red-600">
      <LogOutIcon className="mr-2 w-4 h-4" />
      Log out
    </DropdownMenu.Item>
  </DropdownMenu.Content>
</DropdownMenu>
```

### Hub Selector

For users with hub access:

```tsx
<DropdownMenu>
  <DropdownMenu.Trigger className="flex items-center gap-2">
    <Avatar src={hub.logo} alt={hub.name} size="sm" />
    <span className="font-medium">{hub.name}</span>
    <ChevronDownIcon className="w-4 h-4 text-gray-400" />
  </DropdownMenu.Trigger>

  <DropdownMenu.Content>
    {userHubs.map(hub => (
      <DropdownMenu.Item key={hub.id}>
        <Avatar src={hub.logo} alt={hub.name} size="xs" className="mr-2" />
        {hub.name}
      </DropdownMenu.Item>
    ))}
    <DropdownMenu.Separator />
    <DropdownMenu.Item>
      <PlusIcon className="mr-2 w-4 h-4" />
      Create new hub
    </DropdownMenu.Item>
  </DropdownMenu.Content>
</DropdownMenu>
```

---

## Responsive Behavior

### Mobile Navigation

```tsx
// Mobile menu trigger (in header)
<Button variant="ghost" size="icon" className="md:hidden" onClick={toggleMenu}>
  <MenuIcon />
</Button>

// Mobile menu (slide out)
<div className={`fixed inset-0 z-50 ${isOpen ? 'block' : 'hidden'}`}>
  <div className="absolute inset-0 bg-black/50" onClick={closeMenu} />
  <nav className="absolute right-0 top-0 bottom-0 w-80 bg-white p-6">
    <div className="flex justify-end mb-6">
      <Button variant="ghost" size="icon" onClick={closeMenu}>
        <XIcon />
      </Button>
    </div>
    <div className="space-y-4">
      <a href="/experiences" className="block text-h4">Experiences</a>
      <a href="/hubs" className="block text-h4">Hubs</a>
      <a href="/experts" className="block text-h4">Experts</a>
    </div>
  </nav>
</div>
```
