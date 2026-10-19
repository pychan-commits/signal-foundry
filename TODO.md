# SignalFoundry Phase 1 delivery checklist

- [ ] **Anonymous access and privacy boundary**
  - The shipped website must not expose sign-in, sign-up, login, register, password-reset, Clerk, account, collaborator, invitation, or logout UI/routes.
  - The landing CTA opens `/app` directly; the server issues a secure HTTP-only anonymous browser-session cookie and creates an isolated fictional demo workspace.
  - Session records store only a token hash plus expiry, privacy-notice acknowledgement/version, and separate analytics-consent state. Clearing cookies or losing the session is clearly described as unrecoverable across devices.
  - PICS/privacy notice appears before customer/contact entry and warns against highly sensitive personal data; analytics consent defaults off.

- [ ] **Session-scoped workspace management**
  - Create, rename, switch, export, reset and permanently delete workspaces owned by the current anonymous browser session.
  - Enforce Free workspace limits server-side; reject client-supplied workspace IDs that belong to another session.
  - Reset/delete requires exact-name confirmation and leaves no recoverable records in the active session.

- [x] **Core evidence workflow**
  - Persist and CRUD fictional segments, customers, signals, problems, opportunities, experiments, decisions, tags, notes and saved views.
  - Keep data scoped to the selected workspace and validate all linked records within that workspace.
  - Capture signal source/type/date/customer/segment/urgency/product area/status/body and support search, sorting, combined filters and saved views.
  - Link signals to problems and problems to opportunities; preserve source history when optional links are removed.

- [x] **Transparent prioritisation**
  - Use visible 1–5 frequency, severity, willingness to pay, strategic fit and estimated effort inputs.
  - Use editable default weights of 25/25/20/20/10 and inverse effort scoring `(6 - effort) / 5`.
  - Show formula, component values, score, rank, evidence counts, linked problems and table/matrix alternatives.

- [ ] **Attachments and provider boundaries**
  - When ImageKit is configured, issue server-side signed upload parameters with type/size limits and workspace ownership checks; otherwise show an explanatory disabled state.
  - Founder Stripe checkout may run in test mode only after server configuration checks and verified webhook entitlement; Team remains planned/not purchasable.
  - Gemini is unused in Phase 1 and its key is never required for startup.

- [x] **Desktop-first workspace UX**
  - Dashboard, signals inbox, problems board, opportunities, prioritisation, experiments, decisions, segments, customers, settings and pricing views are available with responsive layouts, keyboard-friendly controls, visible focus states, loading/empty/validation/error/success feedback, and accessible table alternatives.
  - Demo records are fictional and visibly labelled.

- [ ] **Release verification**
  - Add anonymous session-isolation, scoring, validation, prohibited-route, provider-guard and reset/export tests.
  - Run migrations/seed idempotently, typecheck, tests, production build and full primary-journey verification.
  - Search source and logs for prohibited auth routes, hardcoded secrets, raw tokens, contact details, signal bodies and Gemini calls.
