# LexTime

**Offline Legal Practice & Client Money** — a single-file, privacy-first practice management app for advocates, attorneys, and solicitors. Everything runs in the browser: no server, no account, no internet required. Your data stays on your device unless you choose to share it via a workspace file or encrypted backup.

> **License:** AGPL-3.0 · **Platform:** Browser (Chrome, Edge, Firefox) · **Dependencies:** none · **Data:** 100% local

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Screenshots](#screenshots)
- [Quick Start](#quick-start)
- [Usage Guide](#usage-guide)
  - [Clients](#clients)
  - [Matters](#matters)
  - [Timer](#timer)
  - [Time Entries & Expenses](#time-entries--expenses)
  - [Client Money](#client-money)
  - [Invoices](#invoices)
  - [Receipts](#receipts)
  - [Reports](#reports)
  - [Backup & Integrity](#backup--integrity)
  - [Settings](#settings)
- [Data & Privacy](#data--privacy)
- [Tax Behaviour by Jurisdiction](#tax-behaviour-by-jurisdiction)
- [Data Model](#data-model)
- [Architecture & Technical Notes](#architecture--technical-notes)
- [Security Model](#security-model)
- [Working Across Devices](#working-across-devices)
- [Browser Support](#browser-support)
- [Troubleshooting & FAQ](#troubleshooting--faq)
- [Development & Contributing](#development--contributing)
- [Changelog](#changelog)
- [Limitations](#limitations)
- [License](#license)

---

## Overview

LexTime combines the everyday workflows of a legal practice into one tool:

- **Clients & matters** — track matters, billing arrangements, and client contact details.
- **Time tracking** — a resilient timer plus manual time entries.
- **Disbursements / expenses** — billable to the client or internal.
- **Invoicing & payment receipts** — with jurisdiction-aware tax (GST, VAT, sales tax, or custom).
- **Client money** — regulated 3-way accounting for client funds (client ledger, office books, bank) per jurisdiction rules.
- **Reports** — productivity, receivables aging, and client-money balances, with CSV export.
- **Backup & integrity** — encrypted backups, concurrent offline workspace files, schema validation, and an immutable hash-chained audit log.

All data lives in the browser's `localStorage` and, if you choose, in a JSON workspace file that can be moved between computers.

---

## Features

### Dashboard

- Outstanding balance, overdue invoices, unbilled time & costs, and time logged this month.
- Recent time entries and overdue invoice lists.

### Clients

- Full contact profile, GSTIN / tax number, address, and contact person.
- Per-client outstanding totals and client funds held.
- Search across clients.
- Deletion is blocked while matters or ledger activity exist.

### Matters

- Billing types: **hourly**, **fixed fee**, or **contingency**.
- Live **matter ledger**: time, expenses, invoice issues, and payments roll into a running balance.
- Per-matter unbilled value (ready to invoice), outstanding balance, and client funds held.
- Filter by status (all / open / closed) and by client.

### Timer

- Runs even when the app is closed; resumes on unlock.
- Start, pause, stop & save, discard, and save-and-reset.
- Auto-sets rate from the matter's hourly rate; tagged with the selected solicitor.
- Sessions under one minute are not saved.

### Time Entries & Expenses

- Filter by search, matter, solicitor, and month; totals shown.
- Billable/non-billable flags; billable items flow into the invoice builder.
- Start/end time auto-calculates duration.
- Expenses categorized (court fees, filing, courier, travel, expert charges, etc.).

### Client Money (3-Way Accounting)

Every transaction posts balanced journal entries across three books:

| Side     | Accounts                                    |
|----------|---------------------------------------------|
| **Bank** | Client Bank, Office Bank                    |
| **Client** | Client Ledger (liability to the client) |
| **Office** | Fees Earned, Disbursements / Costs      |

- **Receive client funds** → debit Client Bank, credit Client Ledger.
- **Transfer to office (earned fees)** → debit Client Ledger, credit Fees Earned, move cash from Client Bank to Office Bank.
- **Disburse from client funds** → debit Client Ledger, credit Client Bank.
- **Office expense** → debit Disbursements / Costs, credit Office Bank.
- Guards against overdrawing client funds or client bank balances.
- Regulatory note per jurisdiction is shown inline (India: segregated bank account; USA: IOLTA-style trust funds; UK: SAR separate client account).

### Invoices

- Builder picks unbilled billable time + expenses by matter, with manual lines.
- Tax engine: India GST (CGST+SGST intra-state / IGST inter-state; rates 0/5/12/18), UK VAT (0/5/20), US (no tax by default), or custom.
- Numbering: `INV-YYYY-0001` (issued), `DRF-YYYY-0001` (draft), receipts `RCT-YYYY-0001`.
- Draft saving, voiding, regeneration when linked entries change, print/PDF (print CSS included).
- Payments: manual, "mark fully paid", and paying from client money (which automatically posts the transfer-to-office journal).
- Amount in words on the printed invoice.

### Receipts

- Each payment generates a formal receipt with amount in words and remaining balance.

### Reports

- Solicitor productivity (hours, billable, billed/unbilled value).
- Receivables aging buckets (not due, 1–30, 31–60, 61–90, 90+ days).
- Outstanding by client; client-money balances.
- One-click CSV export (UTF-8 with BOM, opens cleanly in Excel).

### Backup & Integrity

- **Workspace file** — a flat `.json` file (File System Access API). Every change auto-saves to it. If another machine wrote a newer revision, you get conflict resolution: keep yours, load theirs, or merge (per-record choice). Great for sharing a file across computers.
- **Encrypted backup** — exports everything as an AES-256-GCM + PBKDF2 (120,000 iterations) JSON file (with an RC4 fallback if WebCrypto is unavailable).
- **Automatic integrity** — rolling checksum + schema validation on every save, at unlock, and daily.
- **Immutable audit log** — every add/edit/delete stores before/after snapshots in a hash-chained log (`prevHash` links each entry to the previous one); the chain is verified end-to-end.

### Settings

- Firm profile (name, address, GSTIN/tax no., state, email, phone) — printed on invoices.
- Jurisdiction & terminology: India / US / UK / Other (custom role, tax, currency labels).
- Tax and client-money toggles.
- Solicitor roster (single default auto-assigned).
- PIN security: create/change 4–6 digit PIN; 5 wrong tries → 30-second lockout.

---

## Screenshots

*To be added — capture the Dashboard, Matter ledger, Client Money, and Invoice views and drop them here, or publish a live demo link.*

---

## Quick Start

1. Open `index.html` (or the `.html` file) in a modern browser — Chrome, Edge, or Firefox. No build step, no dependencies, no server.
2. On first run, create a 4–6 digit PIN.
3. Explore the sample data via **Settings → Load sample data** to see everything populated, then wipe it when you're ready to go live.

> The File System Access API (workspace files) works best in Chrome/Edge. In other browsers, use **Backup & Integrity → Export/Restore** instead.

---

## Usage Guide

### Clients

Go to **Clients → Add client**. Fill in the name (required), contact person, phone, email, GSTIN/tax number, and address. Each client card shows the number of matters and the total outstanding balance. Click a client to see their matters, client funds held, and contact details.

### Matters

Create a matter under a client. Choose a billing type:

- **Hourly** — set a rate per hour; used by the timer and manual entries.
- **Fixed fee** — a flat fee for the matter.
- **Contingency** — percentage-based; billable time is tracked but not automatically valued.

The matter ledger aggregates all activity (time, expenses, invoices, payments) into a running debit/credit balance. Use the **Invoice this matter** button to jump straight into the invoice builder pre-filtered to that matter.

### Timer

Open **Timer**, select a matter, optionally add a work note and a solicitor, then **Start**. The timer keeps running even if you close the app — it resumes when you unlock. Pause keeps the accumulated time; Stop & save creates a time entry; Save & reset saves and clears the session for the next task.

### Time Entries & Expenses

- **Time entries:** pick a matter, date, start/end times (duration auto-computes), minutes, description, solicitor, rate, and billable flag.
- **Expenses:** pick a matter, date, category, amount, description, and whether it is billable to the client.
- Both views support filtering and show running totals. Billable, uninvoiced items are available in the invoice builder.

### Client Money

Open **Client Money**. The top row shows four key balances: client bank balance, client ledger (owed to clients), office bank balance, and total client funds held.

Use the transaction form to post:

- **Receive client funds** — when you take an advance or deposit from a client.
- **Transfer to office (earned fees)** — when fees are earned and you move money from the client account to the office account.
- **Disburse from client funds** — for client-approved payments (court fees, expert fees, etc.) made from client money.
- **Office expense** — firm money spent (not client money).

Each transaction posts balanced journal entries across the client ledger, office ledger, and bank books. Recent journal entries are listed below with expandable debit/credit detail.

### Invoices

1. **New invoice** → select a matter.
2. Tick the unbilled billable time and expenses to include (or **Add all**).
3. Add manual lines if needed (e.g., flat fee items).
4. Choose tax rate and type (jurisdiction-aware) or opt out of tax for this invoice.
5. **Save draft** or **Issue invoice**.

Issued invoices get a number (`INV-YYYY-0001`), set a 15-day due date, and appear in the Dashboard/Invoices lists. From an invoice you can print/PDF it, record a payment (including from client money), regenerate it if linked entries changed, or void it (which un-marks its items as billable).

### Receipts

Recording a payment on an invoice automatically creates a receipt (`RCT-YYYY-0001`). **Receipts** lists all of them. Open a receipt to view and print the formal receipt document.

### Reports

**Reports** aggregates:

- Total hours and billable hours.
- Invoiced (net) and unbilled value.
- Client-money balances (when enabled).
- Solicitor productivity bars.
- Receivables aging buckets.
- Outstanding by client.
- Data integrity summary.

Use **Export CSV** to download a CSV with solicitor productivity, outstanding-by-client, and client-money tables.

### Backup & Integrity

See [Working Across Devices](#working-across-devices) and the [Backup & Integrity](#backup--integrity) feature section for the full workflow.

### Settings

Configure your firm profile (printed on invoices), jurisdiction and terminology, tax/client-money toggles, the solicitor roster, PIN security, and data tools (sample data, erase all).

---

## Data & Privacy

- **100% offline.** Nothing is sent to any server. Data persists in the browser's `localStorage` under key `lexbill_db_v1` (metadata in `lexbill_meta_v1`).
- **Clearing browser data erases it.** Export an encrypted backup or use a workspace file for anything you need to keep.
- **Backups are encrypted** before download; only the password you supply can decrypt them.
- The app checks integrity automatically and will warn you if the data ever fails its checksum or schema validation.

---

## Tax Behaviour by Jurisdiction

| Country | Role label | Tax | Default rates | Notes |
|---------|-----------|-----|---------------|-------|
| India (`IN`) | Advocate | GST | 0 / 5 / 12 / 18% | CGST+SGST (intra-state) or IGST (inter-state); state code on invoices |
| United States (`US`) | Attorney | Sales tax | 0% | Legal services exempt in most states; EIN/TIN field shown |
| United Kingdom (`GB`) | Solicitor | VAT | 0 / 5 / 20% | VAT registration number appears on invoices |
| Other | Custom | Custom | 0 / 5 / 10 / 18% | Define role, tax label, tax-number label, and currency codes |

Tax can be disabled globally (Settings) or per-invoice in the builder.

---

## Data Model

LexTime persists a single schema-versioned database object (currently **version 2**):

| Collection | Purpose |
|------------|---------|
| `settings` | Firm profile, jurisdiction, tax/client-money toggles, PIN hash, solicitor roster |
| `clients` | Client records (name, contact, GSTIN, address) |
| `matters` | Matters with billing type, rates, status, open/close dates |
| `timeEntries` | Time worked (minutes, rate at entry, billable, invoiced flag) |
| `expenses` | Disbursements (category, amount, billable, invoiced flag) |
| `invoices` | Invoices with items, tax split (CGST/SGST/IGST/VAT), payments, status |
| `ledgerEntries` | Journal lines (side: bank/client/office, direction, account, amount) |
| `journalMeta` | Journal headers (date, description, reference, linked client/matter) |
| `auditLog` | Hash-chained immutable audit trail (before/after snapshots) |
| `seq` | Invoice / receipt / journal counters |

Referential integrity is enforced: time/expense/invoice records must reference existing matters; invoice items reference original entries; ledgers reference valid clients/matters; payments cannot exceed invoice totals.

---

## Architecture & Technical Notes

- **Stack:** plain HTML/CSS/JS in a single self-contained file. No frameworks, no external assets, no network requests.
- **Persistence:** `localStorage` with a `memStore` fallback when storage is blocked; all writes wrapped in try/catch.
- **Hashing:** `cyrb53` (non-cryptographic, deterministic) for PIN hashes, audit-log chaining, and integrity checksums.
- **Rendering:** a lightweight DOM-rendering layer via template strings + `innerHTML`; state machine over `state.view` / `state.param`.
- **Print CSS:** sidebar/topbar/modals are hidden, so invoices and receipts print cleanly.
- **Number formatting:** locale-aware (`en-IN`), with currency resolved from jurisdiction (INR/USD/GBP/custom).
- **Amount in words:** Indian numbering convention (lakh/crore) used across all currencies' invoice/receipt documents.

---

## Security Model

- **App lock:** a 4–6 digit PIN protects access. The PIN is stored only as a salted `cyrb53` hash, never in plain text.
- **Lockout:** after 5 incorrect attempts the app locks for 30 seconds.
- **Audit chain:** every mutation is appended to a hash-chained log; tampering breaks the chain and is detected during integrity checks.
- **Integrity checksums:** a rolling digest of all business data is stored and verified automatically on save, at unlock, and daily.
- **Encrypted backups:** AES-256-GCM with a key derived from your password via PBKDF2 (120,000 iterations, random salt, random IV). Without the password, the backup cannot be read.
- **No network egress:** the app contains no network calls; data never leaves the browser.

---

## Working Across Devices

### Workspace file (recommended for same-user multi-device)

1. On device A: **Backup & Integrity → Create workspace file** and save `lex-workspace.json` (e.g., to a sync folder like OneDrive/Dropbox).
2. Every subsequent change auto-saves to that file (File System Access API).
3. On device B: **Backup & Integrity → Open workspace file** and pick the same file.
4. If device B has written a newer revision, the app detects the conflict and asks whether to keep your version, load the file version, or merge (with per-record choice). The chosen result is written back to the file.

### Encrypted backup (portable archive)

- **Export encrypted backup** — requires a password (min 8 chars), produces a `.json` file that can be stored anywhere.
- **Restore from backup** — replaces all current data after validating structure and schema.

---

## Browser Support

| Feature | Chrome / Edge | Firefox | Safari |
|---------|---------------|---------|--------|
| Core app (PIN, data, invoices, ledger) | ✅ | ✅ | ✅ |
| Workspace file (File System Access API) | ✅ | ⚠️ limited | ⚠️ limited |
| Strong encrypted backup (WebCrypto) | ✅ | ✅ on HTTPS/localhost | ✅ on HTTPS/localhost |

For browsers without File System Access API, use **Export/Restore encrypted backup** for portability. WebCrypto requires a secure context (`https://` or `localhost`); otherwise the app falls back to a legacy RC4-based backup format.

---

## Troubleshooting & FAQ

**Where is my data stored?**
In your browser's `localStorage` (keys `lexbill_db_v1` / `lexbill_meta_v1`). It never leaves your device.

**I cleared my browser data and everything is gone.**
Yes — clearing site data erases the local database. Restore from an encrypted backup or a workspace file. This is why regular backups matter.

**The workspace file won't save / the "Open workspace file" button does nothing.**
Use Chrome or Edge (File System Access API). In other browsers, use **Backup & Integrity → Export/Restore**.

**Restore says "Strong crypto unavailable".**
WebCrypto (AES-GCM) requires a secure context. Serve the file over `localhost` or HTTPS, or use a Chromium browser.

**I forgot my PIN.**
The PIN cannot be recovered (only a hash is stored). To start fresh, clear the site data for LexTime — this erases the database, so restore from a backup to keep your data.

**An invoice's amounts look stale.**
Time/expense entries linked to the invoice were edited. Click **Regenerate** on the invoice to refresh amounts and totals.

**Can I undo a deletion?**
The audit log records before/after snapshots for every change. For data recovery, use backups. (Deleting matters/clients with dependent records is blocked to prevent accidental data loss.)

**Does the timer keep running if I close the tab?**
Yes — as long as the browser/profile session persists, the timer is tracked via saved metadata and resumes when you reopen the app.

**Why is the ledger balance different from "Outstanding"?**
The matter ledger reflects all activity (including uninvoiced time/expenses); invoice outstanding reflects only issued invoices minus payments. They are different views by design.

---

## Development & Contributing

LexTime is intentionally a single-file application. To contribute:

1. **Clone or download** the repository.
2. Open `index.html` in your browser to run it locally.
3. Make changes in the single HTML file (HTML/CSS/JS sections are clearly separated).
4. Test on Chrome/Edge (workspace file + WebCrypto) and Firefox.
5. Submit a pull request with a clear description of the change and how it was tested.

Guidelines:

- Keep the app fully offline: no CDNs, no external assets, no network calls.
- Preserve the privacy model: data stays on-device.
- Keep the audit log and integrity checks working — any new mutation should go through `auditLogAdd()` and `recordIntegrity()`.
- Maintain backward compatibility with the versioned schema (`normalizeParsed`).
- Test both tax-enabled and tax-disabled paths, and all jurisdictions (IN/US/GB/OTHER).

---

## Changelog

### 1.0.0
- Initial release.
- Clients, matters, time tracking (timer + manual entries), expenses.
- Invoicing with jurisdiction-aware tax (India GST / UK VAT / US / custom), drafts, voiding, payment recording.
- Payment receipts with print view.
- Client money 3-way ledger (receive, transfer to office, disburse, office expense).
- Reports (solicitor productivity, receivables aging, outstanding by client, client money) with CSV export.
- Backup & integrity: encrypted backups (AES-256-GCM + PBKDF2), concurrent offline workspace files with conflict resolution, schema validation, immutable hash-chained audit log.
- Settings: firm profile, jurisdiction & terminology, tax/client-money toggles, solicitor roster, PIN lock.

---

## Limitations

- The workspace file feature requires a Chromium-based browser (File System Access API). See **Backup & Integrity → Workspace file** for alternatives.
- Data is local to one browser profile; use workspace files/encrypted backups to work across devices.
- The encrypted backup uses WebCrypto when available; on non-secure (`http`) contexts without `crypto.subtle`, it falls back to a legacy RC4-based format (still password-derived, but weaker — prefer HTTPS/localhost for real data).
- Multi-user concurrent editing uses an optimistic, revision-based conflict model (latest revision wins by default, with per-record merge choices). It is **not** a real-time collaboration tool.
- The app provides bookkeeping and record-keeping support but is not a substitute for professional accounting or legal advice. Verify compliance with your jurisdiction's conduct rules, client-money regulations, and tax obligations.

---

## License

Copyright © 2026 — LexTime contributors.

This program is free software: you can redistribute it and/or modify it under the terms of the **GNU Affero General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.**

This program is distributed in the hope that it will be useful, but **WITHOUT ANY WARRANTY**; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU Affero General Public License for more details.

You should have received a copy of the GNU Affero General Public License along with this program. If not, see <https://www.gnu.org/licenses/agpl-3.0.html>.

**Note for network use:** under AGPL-3.0, if you modify this software and offer it to users over a network (e.g., as a hosted web service), you must make your modified source code available to those users under the same license. This includes any server-side deployment of LexTime, even if it runs entirely in the client browser.
