# G-Tally

**A Chrome extension that tracks total time spent on Google Calendar event blocks — live, in the browser, with no login required.**

G-Tally injects a floating widget directly onto Google Calendar. It reads your event blocks, groups them by name, and shows you how many hours you've allocated to each activity across the week — in real time.

![G-Tally Demo](./Assets/g-tally-timetracker-demo.gif)


## Features

- **Live time totals** — see hours per event type for the current week view
- **Auto-refresh** — updates automatically when you navigate to a different week
- **Draggable widget** — move it anywhere on screen
- **Minimizable** — collapse to a small pill when not needed
- **No account required** — reads only what's visible in your browser
- **No data collection** — nothing is stored, logged, or transmitted


## Installation

G-Tally is not yet on the Chrome Web Store. Install it in developer mode — this is safe and takes about 90 seconds.

### Requirements

- Google Chrome 88 or later (or any Chromium-based browser)
- A Google Calendar account

### Steps

**1. Download the extension**

Go to the [Releases page](../../releases) and download the latest `g-tally.zip`. Unzip it to a permanent location on your computer — for example `Documents/extensions/g-tally`. Don't delete this folder after installing.

**2. Open Chrome Extensions**

In Chrome, navigate to:
```
chrome://extensions
```

**3. Enable Developer Mode**

Toggle the **Developer mode** switch in the top-right corner of the Extensions page.

**4. Load the extension**

Click **Load unpacked**, then select the `g-tally` folder you unzipped in Step 1.

**5. Open Google Calendar**

Go to [calendar.google.com](https://calendar.google.com). If it was already open, refresh the tab. Switch to **Week view** for best results.

The G-Tally widget appears in the bottom-right corner within 2–3 seconds.

> **Tip:** Set Google Calendar to start the week on Monday for a clean Mon–Sun summary.  
> Go to Settings → General → Start week on → Monday.


## Usage

| Control | Action |
|---|---|
| **↻ Refresh** | Re-scans all events. Briefly scrolls the calendar to load all days before counting. |
| **− Minimize** | Collapses widget to a header pill. Click **＋** to expand. |
| **Drag header** | Repositions the widget anywhere on screen. |
| **Auto-refresh** | Fires automatically when you navigate to a different week. |

### Best views

| View | Result |
|---|---|
| Week view | ✅ Best — shows all 7 days, auto-scroll loads hidden events |
| Day view | ✅ Great — full detail for a single day |
| Month view | ⚠️ Limited — GCal truncates events, time ranges may not parse |
| Schedule view | ⚠️ Limited — different DOM structure, some events may be missed |


## How It Works

G-Tally reads event data directly from the Google Calendar DOM — no API calls, no authentication.

Google Calendar renders each event block as a `div` with class `.GTG3wb` (timed events) or `.vEJ0bc` (all-day events). The `innerText` of each block contains the event's time range and title in a consistent comma-separated format:

```
"9am to 10am, Meeting name, user@email.com, No location, March 2, 2026"
```

G-Tally parses the first line of each chip, extracts the time range and title, calculates the duration in minutes, and aggregates totals by event name.

### Key implementation details

- **Deduplication** — Google Calendar sometimes renders the same event in multiple DOM nodes. G-Tally uses a `Set` to skip already-seen event strings.
- **Force-render scroll** — GCal uses virtual rendering and only draws event chips in the viewport. On refresh, G-Tally briefly scrolls the grid top → bottom → back to force all days to render before counting.
- **Observer pause** — The `MutationObserver` is disconnected during the scroll/scrape cycle to prevent it from triggering an infinite refresh loop, then reconnected afterward.
- **URL-based auto-refresh** — Rather than watching all DOM mutations (which would be noisy), the observer only fires when Google Calendar's URL changes — i.e. when the user navigates to a different week.

---

## File Structure

```
g-tally/
├── manifest.json       Chrome Extension Manifest V3 config
├── content.js          DOM scraping, time parsing, widget logic
├── widget.css          Widget styles
├── popup.html          Toolbar icon popup
└── icons/
    ├── icon16.png
    ├── icon48.png
    └── icon128.png
```

---

## Known Limitations

- **Google Calendar DOM changes** — G-Tally targets CSS class names (`.GTG3wb`, `.vEJ0bc`) that Google controls and may rename without notice. If the widget stops finding events, check for an updated release.
- **Week and Day view only** — Month and Schedule views use different rendering and may produce incomplete results.
- **English locale** — Time parsing is tested against English-format time strings (`9am`, `10:30pm`). Other locales may produce incorrect results.
- **Chromium only** — Built and tested in Chrome. Not supported on Firefox or Safari.

---

## Troubleshooting

**Widget doesn't appear**  
Refresh the Google Calendar tab. If it still doesn't show, go to `chrome://extensions`, find G-Tally, and click the ↻ reload icon.

**Count is lower than expected**  
Click the **↻ Refresh** button in the widget. This triggers the auto-scroll to load all days into the DOM before counting.

**Widget shows "No timed events found"**  
Switch to Week or Day view. G-Tally works best when event blocks are visible with time ranges displayed.

**Two events with the same name are grouped together**  
This is by design. G-Tally groups by exact event title. Rename events to distinguish them (e.g. `Work — Client A` vs `Work — Client B`).

---

## Privacy

G-Tally runs entirely in your browser. It does not collect, transmit, store, or share any data. No analytics. No tracking. No network requests.

See the full [Privacy Policy](PRIVACY.md).

---

## Roadmap

- [ ] Chrome Web Store release
- [ ] Support for custom date ranges
- [ ] Export totals to CSV
- [ ] Firefox / Edge compatibility
- [ ] Multi-language time format support

---

## About

Built by **Rob Whyte**, technical writer based in Busan, South Korea.

This project was created as a technical writing portfolio piece demonstrating end-to-end documentation: building the software, identifying edge cases through real-world testing, and writing clear documentation for both end users and technical audiences.

- Portfolio: [robwhyte.com](https://robwhyte.com)
- LinkedIn: [linkedin.com/in/robwhyte](https://linkedin.com/in/robwhyte)

---

## License

MIT License — free to use, modify, and distribute.