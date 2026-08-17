# inquiry-form — Field Inquiry Form

Arabic RTL form used by Rizkalla field inquiry agents. It is a static, dependency-free
site (`index.html`) intended for GitHub Pages.

## Submission flow

The form mirrors Smartsheet form **3009578999819** and writes to the
**InquiryDataActive** sheet (`1847861919567748`) through the Firebase HTTPS function:

```text
https://us-central1-rizpay-abb7d.cloudfunctions.net/inquiryTicketSubmit
```

The function source is in the sibling `customer-installments-portal` repository at
`functions/src/services/inquiryTicket.service.js`. The browser never receives the
Smartsheet API token.

Captured questions:

- Customer full name and 14-digit national ID (required)
- Customer mobile (required, exactly 11 digits) and first-degree relative phone (required)
- Governorate and detailed inquiry address (required)
- Inquiry type (optional, supports multiple choices)
- Inquirer email (required)
- Additional data, inquiry data, and family data (optional)
- Collection place (required, supports multiple choices)
- Visit location (required and captured from device GPS)
- Documents (required; up to 5 files, 5 MB each)

The backend sets the hidden Smartsheet `Type` value to `مستعلم`, writes the Cairo
inquiry date, creates the row, and uploads documents to that row sequentially.

## Location enforcement

- The page requests high-accuracy browser geolocation on load.
- The submit button remains disabled until location is available.
- Location is captured again immediately before every submission so an old page-load
  coordinate is never used.
- The backend validates coordinate ranges and capture freshness, then stores a Google
  Maps URL in the Smartsheet `موقع الزيارة` column.
- The site must run on HTTPS (or localhost) because browsers block geolocation on
  insecure origins.

## Run locally

Serve the folder rather than opening the file directly so browser behavior matches
production:

```bash
python3 -m http.server 5500
```

Then open `http://127.0.0.1:5500` and allow location access.

## Repository

This folder is its own repository:

```text
https://github.com/rizkallaco/inquiry-form.git
```

Do not commit it through the parent workspace repository.
