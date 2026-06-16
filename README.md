# OtaMota Car Show - QR Code Voting Redirect

This repo powers the QR code voting system for the OtaMota Car Show. Each car in the show has a printed sign with a QR code. When an attendee scans the code, they are instantly redirected to a pre-filled Google Form for that car's space number.

**The QR codes never change.** Only the config file needs to be updated each year.

---

## How it works

Each QR code encodes a URL like `https://otamotacarshow.github.io/CS01`. When visited, the page looks up that space ID in `cars.json`, builds the correct Google Form URL, and redirects the attendee automatically.

---

## Files

- `cars.json` - The only file you need to edit each year
- `404.html` - The redirect page served to attendees (do not edit)
- `README.md` - This file

---

## Annual update process

### Step 1 - Create this year's Google Form

Build the new Best in Show voting form in Google Forms as usual. Make sure it includes:
- A field for the car space number
- A field for the consent/agreement acknowledgement

### Step 2 - Get the pre-fill field IDs

1. Open your new form in Google Forms
2. Click the three-dot menu (top right) and choose **Get pre-filled link**
3. Fill in a test value in the car space field (e.g. `CS01`) and check the agreement field
4. Click **Get link** - Google will give you a URL that looks like this:

```
https://docs.google.com/forms/d/e/FORM_ID/viewform?usp=pp_url&entry.XXXXXXXXX=CS01&entry.YYYYYYYYY=I+understand+%26+Agree
```

From that URL you need two things:
- **`form_base_url`** - everything up to and including the `=` sign before `CS01`
- **`form_suffix`** - the `&entry.YYYYYYYYY=I+understand+%26+Agree` part at the end

### Step 3 - Update cars.json

Open `cars.json` and update the two URL fields:

```json
{
  "form_base_url": "https://docs.google.com/forms/d/e/NEW_FORM_ID/viewform?usp=pp_url&entry.NEW_SPACE_FIELD_ID=",
  "form_suffix": "&entry.NEW_AGREEMENT_FIELD_ID=I+understand+%26+Agree",
  "spaces": [
    "CS01", "CS02", "CS03", ...
  ]
}
```

If the space numbers changed from last year, also update the `spaces` list. Add or remove entries as needed. Space IDs must match the QR codes exactly (e.g. `CS01`, not `cs01` or `1`).

### Step 4 - Verify before the event

After saving, double-check your work by constructing a test URL manually:

Take `form_base_url`, add a space ID like `CS01`, then add `form_suffix`. The resulting URL should open your new form with the space number and agreement already filled in.

### Step 5 - Commit and push

```
git add cars.json
git commit -m "Update voting form for [year]"
git push
```

GitHub Pages deploys automatically within a minute or two. The QR codes on the printed signs do not need to change.

---

## Adding new space numbers

If new car spaces are added (e.g. CS41 and beyond), just add the new IDs to the `spaces` array in `cars.json` and generate new QR codes for those spaces using the URL pattern `https://otamotacarshow.github.io/CS41`. Existing QR codes are unaffected.

---

## Troubleshooting

**Attendee scans the code but sees an error page**
- Confirm the space ID in the QR code matches exactly what is in the `spaces` list in `cars.json`
- Make sure the latest `cars.json` has been committed and pushed

**Form opens but the space number is not pre-filled**
- The `form_base_url` in `cars.json` may be cut off too early or too late - re-check Step 2 above

**Form opens but the agreement field is not pre-filled**
- The `form_suffix` may be wrong or missing - re-check Step 2 above
