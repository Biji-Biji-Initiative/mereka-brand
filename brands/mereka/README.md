# Mereka Design System

> The official design system for Mereka products. Use this as the source of truth when building any Mereka-branded UI.

**Figma Source**: [mereka.io Design System](https://www.figma.com/design/jBO2FrTslM4wocrRzwQaPo/mereka.io-Design-System)

---

## Getting Started

New to Mereka? Follow this path:

1. Read this README for context
2. Review **Foundations** (colors, typography, spacing)
3. Explore **Components** relevant to your feature
4. Check **Patterns** for common UI flows
5. Follow **Guidelines** for accessibility and polish

### For New Developers

```bash
# Clone the repo
git clone <repo-url>
cd bbbi-mereka-brand-assets/brands/mereka

# Key files to review first
docs/colors.md      # Brand palette
docs/typography.md  # Font system
docs/buttons.md     # Primary interactions
docs/layouts.md     # Page structure
```

---

## Documentation

### Foundations

Core design tokens and visual language.

| Doc | Description |
|-----|-------------|
| [Colors](docs/colors.md) | Brand palette, semantic colors, gradients |
| [Typography](docs/typography.md) | Fonts, sizes, hierarchy, text styles |
| [Spacing](docs/spacing.md) | Spacing scale, elevation, layout grid |
| [Icons](docs/icons.md) | Icon library, sizes, usage guidelines |

### Components

Reusable UI building blocks.

| Doc | Description |
|-----|-------------|
| [Buttons](docs/buttons.md) | Button variants, states, sizes |
| [Inputs](docs/inputs.md) | Text fields, selects, checkboxes, radios |
| [Cards](docs/cards.md) | Card layouts for experiences, hubs, experts |
| [Navigation](docs/navigation.md) | Headers, footers, menus, breadcrumbs |
| [Tables](docs/tables.md) | Data tables, sorting, pagination |
| [Dialogs](docs/dialogs.md) | Modals, drawers, popovers, tooltips |
| [Components](docs/components.md) | Overview of all components |

### Patterns

Common UI flows and compositions.

| Doc | Description |
|-----|-------------|
| [Patterns](docs/patterns.md) | Search, filtering, booking flows |
| [Forms](docs/forms.md) | Form layouts, validation, multi-step forms |
| [Layouts](docs/layouts.md) | Page templates, responsive grids |
| [Loading](docs/loading.md) | Skeletons, spinners, progress indicators |
| [Notifications](docs/notifications.md) | Toasts, alerts, banners, badges |
| [Errors](docs/errors.md) | Error states, empty states, fallbacks |

### Guidelines

Best practices and standards.

| Doc | Description |
|-----|-------------|
| [Animations](docs/animations.md) | Motion principles, transitions, micro-interactions |
| [Accessibility](docs/accessibility.md) | WCAG compliance, keyboard nav, screen readers |

---

## What is Mereka?

Mereka is a learning and experiences marketplace platform with these core products:

| App | Purpose | Users |
|-----|---------|-------|
| mereka.io | Public landing/discovery | Everyone |
| app.mereka.io | Main application | Learners, Experts |
| checkout.mereka.io | Booking & payments | Learners |
| admin.mereka.io | Platform management | Admins |
| Hub dashboards | Organization management | Hub owners |

### Key Domain Concepts

Understanding these helps you design appropriate UIs:

- **Experience** - Workshops, events, learning sessions (physical/virtual/hybrid)
- **Hub** - Organizations offering experiences (like a business profile)
- **Expert** - Individuals offering services/expertise
- **Learner** - Users booking and attending experiences
- **Booking** - A learner's reservation for an experience
- **Expertise** - 1:1 services offered by experts

---

## Directory Structure

```
mereka/
├── README.md              # This file
├── docs/                  # Human-readable documentation
│   ├── colors.md          # Color system
│   ├── typography.md      # Type scale and fonts
│   ├── spacing.md         # Spacing and elevation
│   ├── icons.md           # Icon library
│   ├── buttons.md         # Button variants and usage
│   ├── inputs.md          # Form inputs
│   ├── cards.md           # Card components
│   ├── navigation.md      # Headers, footers, menus
│   ├── tables.md          # Data tables
│   ├── dialogs.md         # Modals and overlays
│   ├── components.md      # UI components overview
│   ├── patterns.md        # Common UI patterns
│   ├── forms.md           # Form patterns
│   ├── layouts.md         # Page layouts
│   ├── loading.md         # Loading states
│   ├── notifications.md   # Alerts and toasts
│   ├── errors.md          # Error handling
│   ├── animations.md      # Motion design
│   └── accessibility.md   # A11y guidelines
├── tokens/                # Design tokens (CSS, JSON)
│   ├── tokens.css         # CSS custom properties
│   └── design-tokens.json # Machine-readable tokens
├── colors/                # Color definitions
├── logos/                 # Logo files (SVG, PNG)
├── icons/                 # Icon sets
├── fonts/                 # Font files
└── guides/                # Brand guidelines
```

---

## Tech Stack

Mereka products use:

- **React 19** + **Next.js 15**
- **TypeScript**
- **Tailwind CSS**
- **Radix UI** for accessible primitives
- **CVA** (Class Variance Authority) for component variants

Code examples in this repo use this stack.

---

## For AI Agents

This repository is designed to be your source of truth when building Mereka UIs.

### How to Use This Repo

1. **Start with the relevant doc** - Building a form? Read `docs/forms.md` first
2. **Check tokens** - Use `tokens/tokens.css` for exact values
3. **Follow patterns** - Don't reinvent; copy and adapt existing patterns
4. **Respect accessibility** - Check `docs/accessibility.md` for requirements

### Priority Reading Order

```
1. docs/colors.md         # Essential - brand colors
2. docs/typography.md     # Essential - text styling
3. docs/buttons.md        # Most common component
4. docs/inputs.md         # For any forms
5. docs/layouts.md        # Page structure
6. docs/accessibility.md  # Must-follow guidelines
```

### Key Principles

| Do | Don't |
|----|-------|
| Use semantic colors (`text-primary`) | Use hardcoded hex values |
| Follow the spacing scale (4, 8, 12, 16...) | Make up arbitrary spacing |
| Use defined component variants | Create new button styles |
| Include hover/focus states | Ship without interaction states |
| Use `rounded-lg` for cards | Mix different border radii |
| Use `rounded-full` for buttons | Use `rounded-md` on buttons |

### Common Mistakes to Avoid

- Using black (`#000000`) for text - use the gray scale from `colors.md`
- Making up button colors - use solid, outline, or ghost variants
- Ignoring hover/focus states - all interactive elements need them
- Wrong border radius - cards use `rounded-lg`, buttons use `rounded-full`
- Skipping loading states - check `loading.md` for skeletons and spinners
- Missing error states - check `errors.md` for fallback patterns

### Quick Lookup Commands

```bash
# Find color values
grep -i "teal" docs/colors.md

# Find button examples
grep -A 5 "className" docs/buttons.md

# Find spacing values
grep "px" docs/spacing.md
```

---

## Quick Reference

### Primary Colors
| Name | Hex | Usage |
|------|-----|-------|
| Teal | `#2d898b` | Primary CTAs, brand accent |
| Magenta | `#ab3b78` | Secondary accent |
| Blue | `#295cad` | Links, info states |

### Typography
| Style | Size | Font | Weight |
|-------|------|------|--------|
| H1 | 36px | Lato | Bold |
| H2 | 28px | Lato | Bold |
| Body | 16px | Poppins | Regular |
| Caption | 12px | Poppins | Regular |

### Spacing Scale
```
4px, 8px, 12px, 16px, 20px, 24px, 32px, 40px, 48px, 64px
```

---

## Contributing

When updating this design system:

1. Update Figma first (source of truth)
2. Export any new screenshots
3. Update the relevant docs
4. Update tokens if values changed
