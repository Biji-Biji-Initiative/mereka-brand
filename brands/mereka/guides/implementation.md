# Mereka Implementation Helpers

This page provides implementation-ready snippets derived from the brand palette and typography.

## CSS variables

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

## Tailwind config

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

## Google Fonts

```html
<link href="https://fonts.googleapis.com/css2?family=Lato:wght@100;300;400;700;900&display=swap" rel="stylesheet">
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@100;300;400;500;700;900&display=swap" rel="stylesheet">
```

## Favicons

| Size | File |
| --- | --- |
| 16x16 | `icons/favicon/favicon-16x16.png` |
| 32x32 | `icons/favicon/favicon-32x32.png` |
| 256x256 | `icons/favicon/favicon-256x256.png` |
