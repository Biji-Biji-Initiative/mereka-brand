# BBBI + Mereka Brand Assets

Core repository for Biji-biji Initiative (BBI) and Mereka branding assets: logos, favicons,
print files, templates, and source files. Brand guides live alongside the assets.

## Structure

```
bbbi-mereka-brand-assets/
├── brands/
│   ├── mereka/
│   │   ├── colors/
│   │   ├── fonts/
│   │   ├── guides/
│   │   │   ├── brand-guidelines.pdf
│   │   │   ├── brand-guidelines.md
│   │   │   └── implementation.md
│   │   ├── icons/
│   │   │   └── favicon/
│   │   ├── logos/
│   │   │   ├── png/
│   │   │   └── svg/
│   │   ├── print/
│   │   ├── source/
│   │   └── templates/
│   └── bbi/
│       ├── guides/
│       │   ├── brand-guide-2023.pdf
│       │   ├── brand-guide-2023.md
│       │   ├── logo-usage-2023.pdf
│       │   └── logo-usage-2023.md
│       ├── icons/
│       │   └── favicon/
│       ├── logos/
│       │   ├── png/
│       │   │   ├── primary/
│       │   │   └── legacy/
│       │   └── svg/
│       │       └── primary/
│       └── source/
```

## Brand guides

- Mereka: `brands/mereka/guides/brand-guidelines.md` (text extract) and
  `brands/mereka/guides/brand-guidelines.pdf` (official PDF).
- BBI: `brands/bbi/guides/brand-guide-2023.md` (text extract) and
  `brands/bbi/guides/brand-guide-2023.pdf` (official PDF).
- BBI logo usage: `brands/bbi/guides/logo-usage-2023.md` (text extract) and
  `brands/bbi/guides/logo-usage-2023.pdf` (logo usage subset PDF).

## Usage

Raw GitHub URL example:
```
https://raw.githubusercontent.com/Biji-Biji-Initiative/bbbi-mereka-brand-assets/main/brands/mereka/logos/svg/mereka-logo-black.svg
```

Notes:
- Primary BBI logo assets live under `brands/bbi/logos/png/primary/` and `brands/bbi/logos/svg/primary/`.
- Legacy or working PNGs are kept in `brands/bbi/logos/png/legacy/`.
- Some legacy/print asset filenames contain spaces; URL-encode spaces as `%20` when using raw URLs.

Git submodule:
```bash
git submodule add https://github.com/Biji-Biji-Initiative/bbbi-mereka-brand-assets.git brand-assets
```

## Favicons

### Mereka

| Size | File |
| --- | --- |
| 16x16 | `brands/mereka/icons/favicon/favicon-16x16.png` |
| 32x32 | `brands/mereka/icons/favicon/favicon-32x32.png` |
| 256x256 | `brands/mereka/icons/favicon/favicon-256x256.png` |

### BBI

| Size | File |
| --- | --- |
| 16x16 | `brands/bbi/icons/favicon/bbi-favicon-16x16.png` |
| 32x32 | `brands/bbi/icons/favicon/bbi-favicon-32x32.png` |
| 256x256 | `brands/bbi/icons/favicon/bbi-favicon-256x256.png` |
