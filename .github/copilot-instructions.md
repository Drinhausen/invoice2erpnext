# Copilot Instructions for Invoice2Erpnext

This document provides guidance for AI assistants working on the Invoice2Erpnext Frappe app. It covers project-specific conventions, development workflows, and common pitfalls.

## Build/Test Commands

This is a standard Frappe app. Use the following `bench` commands (run from the Frappe bench directory):

```bash
# Install the app on a site
bench get-app invoice2erpnext /path/to/source
bench --site <site-name> install-app invoice2erpnext

# Run migrations
bench --site <site-name> migrate

# Run tests for the app
bench --site <site-name> run-tests invoice2erpnext

# Start development server
bench start
```

- Tests are written using `frappe.tests.utils.FrappeTestCase`.
- Test files are located alongside each doctype (e.g., `invoice2erpnext/invoice2erpnext/doctype/invoice2erpnext_log/test_invoice2erpnext_log.py`).
- No external dependencies beyond Frappe framework (see `requirements.txt`).

## Architecture

Invoice2Erpnext is a Frappe app that integrates with an external invoice processing API. Key components:

1. **Doctypes**:
   - `Invoice2Erpnext Settings` (Single): Stores API credentials and default accounts/groups.
   - `Invoice2Erpnext Log` (Log): Tracks processing events and errors.

2. **Hooks** (`hooks.py`):
   - Custom JavaScript for Purchase Invoice list view (`doctype_list_js`).
   - No scheduled tasks or document events currently configured.

3. **Utilities** (`utils.py`):
   - `format_currency_value`: Formats currency according to system number format.
   - `check_settings_enabled`: Safely checks if the app is enabled.

4. **Client‑side JavaScript**:
   - `public/js/purchase_invoice_list.js`: Adds a "Process with Invoice2Erpnext" button to the Purchase Invoice list.

5. **Workspace** (`workspace/invoice2erpnext/invoice2erpnext.json`):
   - Defines the app’s workspace in the Frappe desk.

## Project‑Specific Conventions

- **Doctype naming**: Use `invoice2erpnext_` prefix for custom doctypes (e.g., `invoice2erpnext_log`).
- **Settings**: A single‑doctype `Invoice2Erpnext Settings` holds all configuration; always check `enabled` flag before performing operations.
- **Currency formatting**: Use `format_currency_value` from `utils.py` to ensure consistency with system settings.
- **Permission checks**: The `check_settings_enabled` function safely verifies both permissions and the enabled flag.
- **Logging**: All processing steps should create entries in the `Invoice2Erpnext Log` doctype for auditability.

## Potential Pitfalls

1. **API credentials**: The app requires valid API key/secret from KAINOTOMO. Without them, processing will fail.
2. **Frappe version**: This app is built for **ERPNext version 15 and 16**. Ensure the bench environment matches.
3. **Single‑item invoice mode**: When `one_item_invoice` is enabled, a default `Item` must be set in settings.
4. **Currency precision**: Always use `frappe.get_precision("Currency", "amount")` or the helper `format_currency_value` to respect system decimal settings.
5. **Permission errors**: The `check_settings_enabled` function already handles missing read permissions; follow the same pattern when accessing settings.

## Key Files & Directories

```
invoice2erpnext/
├── hooks.py                     # App hooks, desktop, and JS includes
├── utils.py                     # Shared utility functions
├── config/                      # Desktop and documentation config
├── invoice2erpnext/             # Main module
│   ├── doctype/
│   │   ├── invoice2erpnext_log/             # Log doctype (definition, code, tests)
│   │   └── invoice2erpnext_settings/        # Settings doctype (definition, code, tests)
│   └── workspace/               # Workspace definition
├── public/
│   └── js/purchase_invoice_list.js  # Custom list‑view button
└── documentation/               # User‑facing images and sample PDF
```

## Example Prompts for AI Assistants

- “Add a new field `timeout` to the Invoice2Erpnext Settings doctype.”
- “Write a test for the `format_currency_value` utility function.”
- “Create a new doctype `invoice2erpnext_queue` to track pending invoices.”
- “Extend the Purchase Invoice list button to show a progress indicator.”
- “Add a scheduled task that cleans up old log entries every week.”
- “Fix a bug where currency formatting fails when the value is None.”

## How to Update These Instructions

This file is intended to be a living document. When you discover new conventions, pitfalls, or useful patterns, add them here. Keep descriptions concise and link to existing documentation when possible.

---
*Generated for the Invoice2Erpnext Frappe app. Last updated March 2026.*