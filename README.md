# EMS Trucks

Maintenance tracking and fuel reports for the White Horse Fire & EMS trucks.

- **App:** https://emschief.github.io/ems-trucks/ (install from Safari or Chrome with "Add to Home Screen")
- **Owner accounts:** emschief@whitehorsefire.org (Google) and emschief (GitHub). The app stops working if either account is closed.

## How it fits together

| Piece | Where it lives | What it does |
|---|---|---|
| App pages | This repository: `index.html`, `sw.js`, `manifest.webmanifest`, icons | Everything people see. Published automatically by GitHub Pages from the `main` branch. |
| Data script | Apps Script project **EMS Trucks**, owned by emschief (script.google.com → My Projects) | Reads and writes the Google Sheet, checks PINs, stores attachments in Drive. The app calls its web app address (`EXEC_URL` in `index.html`). |
| Data | Google Sheet **Gas Records (Responses)** in emschief's Drive | All trucks, fill-ups, maintenance and attachment records. |
| Google Sites copy | `Index.html` inside the Apps Script project | The same app for sites.google.com/whitehorsefire.org/emstrucks. Kept identical to `index.html` except for three lines (see below). |

Fuel and mileage are entered only on the **Google Form**, which writes to the sheet. The app reports on them; it doesn't add fill-ups.

## The sheet

The script finds data by tab name and column header. **Don't rename these tabs or headers**, or the app will break.

| Tab | Columns the app uses |
|---|---|
| Form Responses 5 (fill-ups from the Google Form) | Timestamp, Truck, Vehicle Odometer Reading, Gallons, Driver |
| Truck | Truck Id, Truck, VIN, License Plate, Registration Expiration, Truck Image |
| Maintenance Log | Maintenance ID, Truck, Service Date, Service Type, Odometer Reading, Service Provider, Work Performed/Noted, Next Service, Next Scheduled Service, Next Service Date, Next Service Mileage |
| Maintenance Attachments | Attachment ID, Maintenance ID, Photo (a Drive path such as `Maintenance Attachments_Images/Attach.20261026-150000.invoice.pdf`) |
| Users | Username, Email, Role |

Adding new columns is fine. Truck photos are in the Drive folder `Truck_Images`; maintenance attachments are in `Maintenance Attachments_Images`.

The sheet's time zone (File → Settings) is Pacific, so Google Form times are 3 hours early. Changing it to Eastern fixes that.

## PINs

- **Crew PIN:** view everything, log maintenance, add attachments.
- **Admin PIN:** also edit trucks and users, delete records, remove attachments, correct fill-ups, change PINs.

The PINs are not in this repository. They are stored in the Apps Script project under Project Settings → Script properties (`CREW_PIN`, `ADMIN_PIN`). Admins can change them in the app under More → Settings.

## How the numbers are worked out

- **Miles for a month or year:** the highest odometer reading in that period minus the highest reading before it. A truck's first period starts from its first reading. A mistyped low reading doesn't inflate the totals; a mistyped high reading does, so fix those in the sheet.
- **Gallons:** every fill-up in the period.
- **Upcoming service:** the most recent record of each service type per truck sets the next service, by date or mileage. *Due soon* means within 15 days or 500 miles; *Overdue* means past it. The truck's current odometer is its highest reading from fill-ups or service records.

## Making changes

### App pages (this repository)
1. Edit `index.html` (all screens, styles and logic are in this one file).
2. If you change anything phones keep offline (icons, manifest), bump `CACHE` in `sw.js` (for example `ems-trucks-v4` → `ems-trucks-v5`) so installed apps pick it up.
3. Commit to `main`. GitHub Pages publishes within about a minute; browsers may show the old version for up to 10 minutes.
4. Copy the same `index.html` into the Apps Script project's `Index.html`, with these differences:
   - add `<base target="_top">` right after `<head>`
   - remove the three lines for `manifest.webmanifest`, `icon-32.png` and `apple-touch-icon.png`

   Then deploy the script (below).

### Data script (Apps Script)
1. Open the EMS Trucks project, edit `Code.gs`, and save.
2. **Deploy → Manage deployments → Edit (pencil) → Version: New version → Deploy.**
   Always edit the existing deployment. A *new* deployment gets a different web address and the app would stop talking to the sheet.
3. If the change adds a new action the app calls, add it to `API_FNS` near the top of `Code.gs`.

### Undoing a change
- **App:** in GitHub, open the commit history, find the last good version, and revert to it.
- **Script:** Deploy → Manage deployments → Edit → pick an older version → Deploy.

### Icons
The black star of life icon set (`icon-*.png`, `apple-touch-icon.png`, `maskable-512.png`) was made from `StarofLife.png` in emschief's Drive. Installed apps only show a new home-screen icon after they are removed and added again.

## History

- AppSheet app "FuelTracker", rebuilt as this free app with no accounts needed.
- Oct 2026: moved to GitHub Pages; star of life icon; switched to maintenance tracking with fuel reports from the Google Form, plus attachments of any file type; black star of life icon.
