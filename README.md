# NYE 2026 Countdown Timer 🎉

A beautiful, responsive New Year's Eve countdown timer built with Vue 3 and Vite. Features a stunning dark/light mode, fireworks celebration at midnight, and automatic timezone detection.

## Features

- ⏱️ Real-time countdown to New Year 2026
- 🌙 Dark/Light mode toggle
- 🎆 Fireworks animation when the countdown reaches zero
- 🌍 Automatic timezone detection
- 📱 Fully responsive design
- ✨ Modern glassmorphism UI with glow effects

---

## Changing the Target Date

To change the countdown target date, edit the `targetDate` constant in `src/App.vue`:

```javascript
// Target date: midnight January 1, 2026 (no timezone suffix, treated as local)
const targetDate = new Date('2026-01-01T00:00:00').getTime()
```

Change `'2026-01-01T00:00:00'` to your desired date in `YYYY-MM-DDTHH:MM:SS` format.

---

## System Requirements

- **Node.js**: v18.0.0 or higher
- **npm**: v9.0.0 or higher (comes with Node.js)

To check your current versions:

```bash
node --version
npm --version
```

---

## Installation

1. Clone or download the repository
2. Navigate to the project directory:
   ```bash
   cd countdown
   ```
3. Install dependencies:
   ```bash
   npm install
   ```

---

## Development Build

To start the development server with hot-reload:

```bash
npm run dev
```

The app will be available at `http://localhost:5173` by default.

---

## Production Build

To create an optimized production build:

```bash
npm run build
```

### Files to Upload

After running the build command, upload the entire contents of the `dist/` folder to your web server:

```
dist/
├── assets/         # CSS and JS bundles (hashed filenames)
├── index.html      # Main HTML entry point
└── vite.svg        # Favicon/assets
```

Simply upload all files and folders inside `dist/` to your web hosting root directory.

### Preview Production Build Locally

To preview the production build before uploading:

```bash
npm run preview
```

---

## License

Made with ❤️ for **Ilerioluwa Legunsen**
