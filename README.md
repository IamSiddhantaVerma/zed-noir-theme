# zed-noir-theme
Pure black Chrome theme with high-contrast white UI and purple accents—lightweight, consistent, and visually striking.


# File Structure:

```diff
/zed-noir-theme
├── images
│   ├── icons
│   │   ├── icon16.png
│   │   ├── icon48.png
│   │   └── icon128.png
│   └── theme
│       ├── theme_frame.png
│       ├── theme_toolbar.png
│       ├── theme_ntp_background.png
│       ├── theme_button_background.png
│       └── theme_window_control_background.png
├── constants.js
└── manifest.json
```

## 1. constants.js

```javascript
// Centralized color definitions for easy maintenance
const COLORS = {
  PURE_BLACK: [0, 0, 0],
  PURE_WHITE: [255, 255, 255],
  ACCENT_PURPLE: [127, 0, 255]
};

// Reusable theme configuration
const THEME_CONFIG = {
  IMAGES: {
    theme_frame: "images/theme/theme_frame.png",
    theme_toolbar: "images/theme/theme_toolbar.png",
    theme_ntp_background: "images/theme/theme_ntp_background.png",
    theme_button_background: "images/theme/theme_button_background.png",
    theme_window_control_background: "images/theme/theme_window_control_background.png"
  },
  PROPERTIES: {
    ntp_background_alignment: "center",
    ntp_background_repeat: "no-repeat",
    ntp_logo_alternate: 1
  },
  TINTS: {
    buttons: [1.0, 1.0, 1.0],   // White UI icons
    frame_incognito: [0.0, 0.0, 0.0],
    background_tab: [0.0, 0.0, 0.0]
  }
};

module.exports = { COLORS, THEME_CONFIG };
```

## 2. manifest.json

```json
{
  "manifest_version": 3,
  "name": "Zed Noir: Ultra Dark",
  "short_name": "Zed Noir",
  "version": "1.0.3",
  "description": "Pure black Chrome theme with high-contrast white UI and purple accents—lightweight, consistent, and visually striking.",
  "author": "Siddhanta Verma",
  
  // Recommended metadata for discoverability
  "homepage_url": "https://github.com/yourusername/zed-noir-theme",
  "repository": "https://github.com/yourusername/zed-noir-theme.git",
  
  "icons": {
    "16": "images/icons/icon16.png",
    "48": "images/icons/icon48.png",
    "128": "images/icons/icon128.png"
  },
  "permissions": ["theme"],
  "theme": {
    "images": "<@import:./constants.js>.THEME_CONFIG.IMAGES",
    "colors": {
      // Frame & Toolbar
      "frame": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "frame_inactive": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "toolbar": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "toolbar_inactive": "<@import:./constants.js>.COLORS.PURE_BLACK",
      
      // Tabs
      "tab_background": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "tab_background_separator": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "tab_text": "<@import:./constants.js>.COLORS.PURE_WHITE",      // Active tab
      "tab_background_text": "<@import:./constants.js>.COLORS.ACCENT_PURPLE",  // Inactive tabs
      
      // Omnibox (Search Bar)
      "omnibox_background": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "omnibox_text": "<@import:./constants.js>.COLORS.PURE_WHITE",
      "omnibox_separator": "<@import:./constants.js>.COLORS.PURE_WHITE",
      
      // New Tab Page
      "ntp_background": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "ntp_text": "<@import:./constants.js>.COLORS.PURE_WHITE",
      "ntp_link": "<@import:./constants.js>.COLORS.PURE_WHITE",
      "ntp_section": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "ntp_section_text": "<@import:./constants.js>.COLORS.PURE_WHITE",
      "ntp_section_link": "<@import:./constants.js>.COLORS.ACCENT_PURPLE",  // Accent links
      
      // UI Elements
      "bookmark_text": "<@import:./constants.js>.COLORS.PURE_WHITE",
      "button_background": "<@import:./constants.js>.COLORS.PURE_BLACK",
      "control_background": "<@import:./constants.js>.COLORS.PURE_BLACK"
    },
    "tints": "<@import:./constants.js>.THEME_CONFIG.TINTS",
    "properties": "<@import:./constants.js>.THEME_CONFIG.PROPERTIES"
  }
}
```

# Implementation Notes:
## 1.Build Process Required:
### -Use a bundler like Webpack with ***webpack-extension-manifest-plugin***
### -Install dependencies:
```bash
npm install webpack webpack-cli webpack-extension-manifest-plugin --save-dev
```
## 2.webpack.config.js:

```javascript
const ManifestPlugin = require('webpack-extension-manifest-plugin');
const baseManifest = require('./manifest.json');
const constants = require('./constants');

module.exports = {
  entry: {
    // Empty entry point for theme-only extension
    background: './empty.js' 
  },
  output: {
    path: __dirname + '/dist',
    filename: '[name].js'
  },
  plugins: [
    new ManifestPlugin({
      config: {
        base: baseManifest,
        extend: {
          theme: {
            images: constants.THEME_CONFIG.IMAGES,
            colors: processColors(constants.COLORS),
            tints: constants.THEME_CONFIG.TINTS,
            properties: constants.THEME_CONFIG.PROPERTIES
          }
        }
      }
    })
  ]
};

// Helper to resolve color references
function processColors(colors) {
  const colorEntries = Object.entries(baseManifest.theme.colors);
  return colorEntries.reduce((acc, [key, value]) => {
    acc[key] = typeof value === 'string' 
      ? colors[value.split('.').pop()] 
      : value;
    return acc;
  }, {});
}
```

# Benefits of This Approach:
## 1.Single Source of Truth:
### -Colors defined in one location
### -Changes propagate throughout the theme
## 2.Human-Readable References:
### -COLORS.PURE_BLACK instead of [0,0,0]
### -ACCENT_PURPLE instead of [127,0,255]
## 3.Easier Customization:
### -Modify colors in constants.js
### -Add new images without restructuring manifest
## 4.Version Control Friendly:
### -Clear diff tracking for color changes
### -Separated configuration from implementation
## 5.Scalable Structure:
### -Add new theme variants easily
### -Support light/dark mode switching (future-proof)

### This structure maintains Chrome extension best practices while significantly improving maintainability. The use of a build process eliminates redundancy and ensures consistency across the theme components.
