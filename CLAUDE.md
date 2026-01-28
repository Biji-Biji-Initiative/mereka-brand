# BBBI + Mereka Brand Assets

## Overview

Core repository for Biji-biji Initiative (BBI) and Mereka branding assets. Contains logos, favicons, print files, templates, source files, and brand guides in multiple formats.

## Directory Structure

```
bbbi-mereka-brand-assets/
├── brands/
│   ├── mereka/
│   │   ├── colors/
│   │   ├── fonts/
│   │   ├── guides/     # Brand guidelines (PDF, MD, YAML, JSON)
│   │   ├── icons/      # Favicons
│   │   ├── logos/      # PNG and SVG
│   │   ├── print/
│   │   ├── source/
│   │   └── templates/
│   └── bbi/
│       ├── guides/     # Brand guide (PDF, MD, YAML, JSON)
│       ├── icons/      # Favicons
│       ├── logos/      # PNG and SVG (primary + legacy)
│       └── source/
```

## Usage

**Raw GitHub URL**:
```
https://raw.githubusercontent.com/Biji-Biji-Initiative/bbbi-mereka-brand-assets/main/brands/mereka/logos/svg/mereka-logo-black.svg
```

**Git Submodule**:
```bash
git submodule add https://github.com/Biji-Biji-Initiative/bbbi-mereka-brand-assets.git brand-assets
```

## Key Files

| Asset Type | Mereka | BBI |
|------------|--------|-----|
| Brand Guide | `brands/mereka/guides/brand-guidelines.md` | `brands/bbi/guides/brand-guide-2023.md` |
| Logo (SVG) | `brands/mereka/logos/svg/` | `brands/bbi/logos/svg/primary/` |
| Logo (PNG) | `brands/mereka/logos/png/` | `brands/bbi/logos/png/primary/` |
| Favicon | `brands/mereka/icons/favicon/` | `brands/bbi/icons/favicon/` |
