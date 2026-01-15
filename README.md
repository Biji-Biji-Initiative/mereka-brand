# Mereka Brand Guidelines

Central repository for Mereka branding assets and guidelines. Use this as a reference for all Mereka projects to ensure consistent design.

## Structure

```
mereka-brand/
├── logos/
│   ├── png/              # PNG logos
│   └── svg/              # SVG logos (scalable)
├── icons/
│   └── favicon/          # Favicon files
├── print/                # EPS files for print (CMYK)
├── source/               # AI source files
├── colors/               # Color palettes
├── fonts/                # Typography
└── brand-guidelines.pdf  # Official brand guide
```

## Logo

The Mereka logo is composed of an **octothorpe** (the connected nodes forming an "M") and a **logotype** set in Lato.

| Variant | Black | White |
|---------|-------|-------|
| **Full Logo** | `mereka-logo-black` | `mereka-logo-white` |
| **Horizontal** | `mereka-logo-horizontal-black` | `mereka-logo-horizontal-white` |
| **Logomark** | `mereka-logomark-black` | `mereka-logomark-white` |

- **Horizontal logo** = primary logo for most situations
- **Stacked logo** = intended for large-scale use
- Always use provided files, do not recreate

## Colors

### Primary Colors

| Color | Hex | Usage |
|-------|-----|-------|
| Black | `#000000` | Primary text, logos |
| White | `#ffffff` | Backgrounds, reversed logos |
| Teal | `#2d898b` | Primary accent |
| Magenta | `#ab3b78` | Primary accent |
| Blue | `#295cad` | Primary accent |

### Secondary Colors

| Color | Hex | Description |
|-------|-----|-------------|
| Burgundy | `#8c002f` | |
| Pink | `#cd89ae` | |
| Light Blue | `#94d1e4` | |
| Orange | `#e18437` | |
| Yellow | `#f4be48` | |
| Seafoam | `#8fbec2` | |
| Periwinkle | `#7f9dce` | |
| Forest Green | `#2c6e49` | |

### CSS Variables

```css
:root {
  /* Primary */
  --mereka-black: #000000;
  --mereka-white: #ffffff;
  --mereka-teal: #2d898b;
  --mereka-magenta: #ab3b78;
  --mereka-blue: #295cad;

  /* Secondary */
  --mereka-burgundy: #8c002f;
  --mereka-pink: #cd89ae;
  --mereka-light-blue: #94d1e4;
  --mereka-orange: #e18437;
  --mereka-yellow: #f4be48;
  --mereka-seafoam: #8fbec2;
  --mereka-periwinkle: #7f9dce;
  --mereka-forest-green: #2c6e49;
}
```

### Tailwind Config

```javascript
colors: {
  mereka: {
    black: '#000000',
    white: '#ffffff',
    teal: '#2d898b',
    magenta: '#ab3b78',
    blue: '#295cad',
    burgundy: '#8c002f',
    pink: '#cd89ae',
    'light-blue': '#94d1e4',
    orange: '#e18437',
    yellow: '#f4be48',
    seafoam: '#8fbec2',
    periwinkle: '#7f9dce',
    'forest-green': '#2c6e49',
  }
}
```

## Typography

| Use | Font | Weights |
|-----|------|---------|
| **Headers & Subheaders** | Lato | Thin, Light, Regular, Bold, Black |
| **Body Text** | Poppins | Thin, Light, Regular, Medium, Bold, Black |
| **Video Names** | Open Sans | Bold |
| **Video Titles** | Lato | Regular |

### Google Fonts Links

```html
<link href="https://fonts.googleapis.com/css2?family=Lato:wght@100;300;400;700;900&display=swap" rel="stylesheet">
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@100;300;400;500;700;900&display=swap" rel="stylesheet">
```

## Video Guidelines

- **Lower thirds**: Clean design, 80% opacity backgrounds
- **Ending slates**: All videos end with Mereka logo
- **Watermarks**: Upper left, 50% opacity

## Usage

**Raw GitHub URLs:**
```
https://raw.githubusercontent.com/Biji-Biji-Initiative/mereka-brand/main/logos/svg/mereka-logo-black.svg
```

**Git submodule:**
```bash
git submodule add https://github.com/Biji-Biji-Initiative/mereka-brand.git brand
```

## Favicons

| Size | File |
|------|------|
| 16x16 | `icons/favicon/favicon-16x16.png` |
| 32x32 | `icons/favicon/favicon-32x32.png` |
| 256x256 | `icons/favicon/favicon-256x256.png` |

## Print Files

EPS files for print (CMYK color space) are in `print/`.

## Do's and Don'ts

- Always use provided logo files
- Do not pair the logomark alone with partner logos
- Do not use Mereka name/marks as part of your own
- Do not use in advertising without explicit approval
