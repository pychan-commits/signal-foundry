# SignalFoundry implementation plan — PRD/tasklist reconciliation

## Product scope
SignalFoundry is an anonymous, desktop-first customer-discovery and product-prioritisation workspace. Visitors open the workspace directly without an account. A server-issued opaque browser-session cookie isolates each visitor's fictional demo workspace. The core journey is signals → problems → opportunities → transparent scoring → experiments → decisions → export/reset.

The implementation keeps the initialized Manus web-db-user runtime (React/Vite, Express/tRPC, Drizzle/MySQL) instead of re-platforming the working project to Next.js/Postgres. The product behavior and security boundaries from the PRD remain authoritative; the existing runtime is the delivery constraint.

## Reconciliation decisions

- Remove all user-facing sign-in, sign-up, password reset, logout, account, invitation, and role-management flows. Existing email-account tables/helpers remain dormant compatibility code only and are not reachable from the UI or route manifest.
- Add an `anonymous_sessions` table and session-scoped cookie bootstrap. A hidden internal subject is used only to reuse the starter's existing workspace query/mutation plumbing; it is never presented as an account or collaborator.
- Add anonymous-session ownership to workspaces and validate the active session before every workspace operation. The session cookie stores only the raw opaque token; the database stores its hash.
- Keep Free, Founder, and Team plan definitions centrally configured. Founder checkout remains test-only and is enabled only when test Stripe configuration exists; Team stays visibly planned and server-disabled.
- Prefer graceful optional-provider behavior. ImageKit attachment capability is exposed as disabled when not configured; Phase 1 never calls Gemini.

## Implementation approach

- **Session/backend:** `server/_core/context.ts` creates/validates the anonymous session and sets a secure HTTP-only cookie. Protected tRPC procedures require the session-backed internal subject. Session metadata records privacy notice acknowledgement and separate analytics consent.
- **Persistence:** Extend the existing Drizzle schema/migration with anonymous sessions, workspace ownership, and provider-safe billing fields. Preserve existing relational demo data and workspace-scoped validation.
- **Frontend:** Remove authentication screens and links; landing CTA opens `/app`. Add accountless-access/privacy copy, reset/export controls, Team planned state, and neutral session menu language. Keep the current quiet evidence studio visual system and desktop-first tables.
- **Billing:** Add central plan configuration and server guards. The current local test-entitlement flow remains as a safe fallback when Stripe is absent; no Team entitlement or live payment path is allowed.
- **Validation:** Add anonymous session isolation tests, scoring boundary tests, prohibited-route checks, and final typecheck/build/test verification. Update route manifest to contain only public, pricing, and app routes.

## Project structure

```text
client/src/App.tsx              Anonymous landing, workspace shell, CRUD views, privacy/session controls
client/src/main.tsx             tRPC client with credentialed requests
client/public/manus-routes.json Public/app route manifest with no auth routes
server/_core/context.ts         Anonymous session bootstrap and request context
server/_core/cookies.ts         Secure Preview-compatible HTTP-only cookie options
server/_core/trpc.ts            Session-required protected procedure
server/db.ts                    Session records, workspace ownership, CRUD, seed, billing guards
server/routers.ts               Session, workspace, data and billing RPCs; no auth UI router
server/plan-config.ts           Central plan/feature/limit definitions
server/anonymous.session.test.ts Session isolation and anonymous access contract tests
drizzle/schema.ts               Anonymous sessions and workspace-owned data schema
drizzle/0002_*.sql               Additive database migration
.env.example                    Safe optional-provider placeholders
TODO.md                         Requirement-level completion and remaining provider limitations
```

## Design direction

Retain **Quiet evidence studio**: warm neutral surfaces, Foundry Indigo `#4F46E5`, dense-but-breathable evidence tables, semantic status labels, direct controls, visible focus rings, and readable score chips. Replace account/profile language with neutral copy such as “Anonymous workspace” and “Browser session”.

## Phase 1 boundaries

No Clerk UI, account recovery, collaboration roles, invitations, Team purchasing, Gemini calls, public sharing, or real customer data. Stripe and ImageKit are optional integrations with explicit disabled states. The app must explain that clearing cookies or losing the anonymous session can make the workspace inaccessible across devices, and must warn against entering highly sensitive personal data.
