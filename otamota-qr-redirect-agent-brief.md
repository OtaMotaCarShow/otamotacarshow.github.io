# OtaMota Car Show - QR Code Redirect Site (GitHub Pages)

## Project Overview

Build a lightweight static site hosted on GitHub Pages that handles QR code redirects for the OtaMota Car Show voting system. Each car in the show has a printed sign with a QR code. Scanning the code should redirect the attendee to a pre-filled Google Form where the car's booth number is already populated, leaving the attendee to only enter their badge number and submit.

---

## Goals

- Each QR code URL must remain **permanent and unchanged** year to year so printed signs can be reused or reprinted with the same codes
- The destination Google Form URL (and pre-fill field mapping) must be **updatable each year by editing a single config file** - no code changes required
- The redirect must be **instant and transparent** to the attendee; they should land directly on the Google Form
- The solution must be **self-contained** and manageable by the event organizer without involving a separate web team

---

## How It Works

Each car's QR code encodes a URL in this format:

```
https://<github-pages-url>/vote?car=01
```

When that URL is visited, the page reads the `car` query parameter, looks up the corresponding pre-filled Google Form URL from a config file, and immediately redirects the browser to it.

---

## File Structure

```
/
- index.html         # Redirect logic (vanilla JS, no frameworks)
- cars.json          # Config file updated each year
- README.md          # Instructions for annual update process
```

---

## cars.json Format

This is the **only file that needs to be edited each year**. It contains the base Google Form pre-fill URL and a mapping of booth numbers to car names (for reference/logging purposes only).

```json
{
  "form_base_url": "https://docs.google.com/forms/d/e/REPLACE_WITH_FORM_ID/viewform?usp=pp_url&entry.REPLACE_WITH_FIELD_ID=",
  "cars": {
    "01": "AE86 Toyota Corolla",
    "02": "Toyota Supra",
    "03": "Nissan Skyline"
  }
}
```

The redirect for car `01` will resolve to:

```
https://docs.google.com/forms/d/e/.../viewform?usp=pp_url&entry.FIELD_ID=01
```

The booth number is what gets pre-filled in the form; the car name in the JSON is just for human readability.

---

## index.html Behavior

- Reads the `car` query parameter from the URL
- Fetches `cars.json`
- Constructs the full Google Form URL by appending the booth number to `form_base_url`
- Redirects via `window.location.replace()` (so the redirect page does not appear in browser history)
- If the `car` parameter is missing or not found in the config, display a simple error message rather than a blank page

---

## Annual Update Process (for the organizer)

Each year the organizer needs to:

1. Create the new Google Form for that year's Best in Show vote
2. Set up the pre-fill URL using Google Forms' "Get pre-filled link" feature to identify the correct `entry.XXXXXX` field ID for the booth number field
3. Update `form_base_url` in `cars.json` with the new form's pre-fill base URL
4. Update the `cars` list in `cars.json` to reflect that year's entrants and booth numbers
5. Commit and push - GitHub Pages deploys automatically

The QR codes on the physical signs do **not** change. Only the config file changes.

---

## QR Code Generation (outside scope of this build)

Once the GitHub Pages URL is live, QR codes should be generated for each booth number URL. This is a one-time step per car per year if booth numbers change, or a one-time step ever if booth numbers stay consistent across years. A bulk QR generator (e.g. a simple script or free online tool) can produce all 40+ codes at once given the URL pattern.

---

## Constraints and Notes

- **No backend required** - this is purely static HTML + JS + JSON
- **No npm, no build step** - keep it plain HTML and vanilla JS so the organizer can edit and understand the repo without a development environment
- **cars.json must be fetched at redirect time** (not hardcoded in JS) so updates to the file take effect immediately on next GitHub Pages deploy without touching `index.html`
- The number of cars is currently ~40 and expected to grow year over year; the solution must scale to 100+ entries without any structural changes
- Do not add any analytics, tracking, or third-party scripts
