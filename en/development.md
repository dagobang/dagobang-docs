# Development & Build

This page provides a minimal build and debugging flow for developers.

## 1. Extension (dagobang-extention)

Enter the directory:

- `cd dagobang-extention`

Install dependencies:

- `npm install`

Dev mode (hot reload for local debugging):

- `npm run dev`

Build MV3 output (for loading into a browser):

- `npm run build`
- Output folder: `.output/chrome-mv3/`

Package as zip (optional):

- `npm run zip`

## 2. Load in Chrome/Edge

- Open extensions page (Chrome: `chrome://extensions/`, Edge: `edge://extensions/`)
- Enable “Developer mode”
- Click “Load unpacked” and select `.output/chrome-mv3/`

## 3. Common Debug Entry Points

- Popup: click the extension icon
- Content UI (floating panel): open a supported site page
- Background: open the service worker devtools from the extensions page

