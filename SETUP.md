# Nuleaf Consumables Tracker — Setup

Two files, same pattern as your other Apps Script tools (payroll, housekeeping tracker):

- `Code.gs` → backend, lives inside a Google Sheet
- `index.html` → frontend, host it on Netlify (or anywhere) same as before

## 1. Create the database sheet
1. New Google Sheet, name it e.g. **"Nuleaf Consumables DB"**.
2. Extensions → Apps Script. Paste in `Code.gs` (replace any boilerplate).
3. Deploy → New deployment → type **Web app**.
   - Execute as: **Me**
   - Who has access: **Anyone**
4. Copy the Web app URL (ends in `/exec`).

The first request auto-creates 4 tabs: `ConsumableLog`, `PurchaseLog`, `Items` (pre-seeded with your 29 items), `Vendors` (pre-seeded with your 3 vendors). You can edit any of these tabs directly in the Sheet too — the app just reads/writes them.

## 2. Connect the frontend
Open `index.html`, find:
```
const API_URL = "PASTE_YOUR_APPS_SCRIPT_WEB_APP_URL_HERE";
```
Paste in the URL from step 1. Save, then host/upload it (Netlify, same as your other tools).

## 3. Re-deploying after edits
Any time you change `Code.gs`: **Deploy → Manage deployments → Edit → New version**. Editing the script alone does not update the live URL.

## What it does
- **Record Usage** — date, patient name, staff name, then a checklist of all 29 items with qty + unit. Only rows with a quantity get saved. Same patient across multiple dates (day 1/2/3 of a transplant) is just multiple separate entries tied to the same patient name — the reports roll these up automatically.
- **Record Purchase** — date, item, qty, unit, cost/item (auto-totals), vendor (dropdown + "add new"), notes.
- **Usage Log / Purchase Log** — filterable tables, CSV export.
- **Reports**:
  - *Cost per Patient* — pick a patient, see item-by-item breakdown and total cost across all their visit dates, plus an all-patients summary table.
  - *Cost per Month* / *Cost per Year* — usage cost vs. purchase cost side by side.
  - *Cost per Vendor* — total spend, quantity, and purchase count per vendor.
  - All four export to CSV.
- **Items & Vendors** — add new consumables or vendors any time (also usable directly from the purchase form).

## How item cost is calculated
There's no fixed price list — cost per unit is the **weighted average cost** for that item across everything you've logged in Purchase Log (`total cost purchased ÷ total quantity purchased`). If an item has never been purchased yet, its usage shows as "no purchase data" until you log at least one purchase for it.

## Notes
- Multiple staff can use it at once — everything writes straight to the shared Sheet, no local-only data.
- "Entered By" remembers the last name typed per device (just a convenience), not tied to login.
- Everything else (patients, items, vendors, all records) lives in the Sheet — you always have raw access there for auditing or backup.
