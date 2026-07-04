# Manawa Shed Records

Standalone mobile PWA for the Bottling Processing Record and Honey Processing
Record. Not connected to the Manawa Honey inventory app or its Supabase
project — this uses its own separate Supabase project ("Manawa Processing +
Bottling").

## What's in this folder
- `index.html` — the whole app (forms, offline queue, cloud sync, CSV export)
- `manifest.json`, `sw.js`, `icon-192.svg`, `icon-512.svg` — PWA install + offline shell

## Deploy (GitHub Pages, same pattern as the inventory app)
1. Create a new GitHub repo (or a folder in an existing one), e.g. `manawa-shed-records`.
2. Upload all 5 files in this folder to the repo root.
3. In the repo: **Settings → Pages → Deploy from branch → main → / (root)**.
4. Open the published URL on a shed-floor phone → **Add to Home Screen**. From
   then on it opens full-screen like an app, and the app shell keeps working
   with no signal.

## How it works
- **One form, two sections.** Batch No sits at the top; Honey Processing and
  Bottling are sections underneath. Fill in one, both, or come back later and
  fill in the other — each section only saves if it has data in it. Under the
  hood they're still stored as separate records (so CSV export/reconciliation
  stays clean), but on the shed floor it's a single scroll.
- Each save writes to the phone's local storage **immediately** — this always
  succeeds, even with zero bars, so nothing is lost mid-batch.
- If online, it also pushes to Supabase right away. If offline, the record sits
  flagged "Pending" and syncs automatically the moment signal returns (checked
  every 30s and on reconnect).
- The **Records** tab shows synced/pending status per record. Tapping a record
  loads that batch's Processing section, Bottling section (if one already
  exists for the same Batch No), or both, so you can correct or complete it.
- **Import CSV**: on the Records tab, upload the original
  `BOTTLING_PROCESSING_RECORD.csv` / `HONEY_PROCESSING_RECORD.csv` (or a CSV
  this app exported) and it's parsed, added locally, and synced to Supabase.
  Columns are matched by header name, so column order doesn't matter. Re-importing
  the same file will add duplicate rows — there's no dedupe check yet.
- **Export CSV** produces the same column headers/order as the original
  `BOTTLING_PROCESSING_RECORD.csv` / `HONEY_PROCESSING_RECORD.csv`, plus one
  extra trailing `Notes` column.
- **Save as PDF**: the button next to Save opens the phone/browser's print
  dialog with a clean, form-shaped summary of the batch (choose "Save as PDF"
  as the destination). No extra app needed — this uses the browser's built-in
  print-to-PDF, so it works offline too.
- Multiple records with the same Batch No are allowed and simply stack (e.g. a
  redo run), matching what the existing Honey Processing history already does.
- Operator names typed in are remembered on-device and offered as suggestions
  next time (autocomplete), so operators don't retype their initials every batch.

## Data / access note
There's no login screen — anyone with the link can enter or edit records. That
was a deliberate speed-over-friction choice for shed-floor entry; say the word
if you'd rather add a lightweight PIN or the same SHA-256 login as the
inventory app.

## Supabase project
- Project: **Manawa Processing + Bottling** (separate from `manawa-honey`)
- Tables: `bottling_records`, `honey_records`, `operators`
- Free tier ($0/month)
