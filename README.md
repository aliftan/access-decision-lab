# Access Decision Lab

A single-page, independent product-rules prototype for the **substation key-reissue** question:

> A contractor has approval until 31 December. He returns his key on Friday and requests it again on Monday. Does he need another approval?

**Default decision:** No new approval is needed *if* the existing authorisation covers the new visit, the site permits reuse, any required safety clearances are valid, and the previous key checkout has been closed. The return closes **key custody**, not **access authorisation**.

The prototype demonstrates how to turn an ambiguous operational question into a clear decision, customer-policy assumptions, edge cases, acceptance tests, and a reusable engineering specification.

## What is in the ZIP?

- `index.html` — complete application: Vue 3 (CDN), Tailwind CSS (CDN), Font Awesome (CDN), embedded CSS, sample data, decision engine, and test fixtures.
- `README.md` — documentation.

No build step, package manager, backend, or API key is needed. The **CDNs require internet access** when users open the deployed site.

## Deploy on GitHub Pages

1. Create a public GitHub repository, for example `access-decision-lab`.
2. Extract the ZIP and put `index.html` and `README.md` in the repository root.
3. Commit and push to `main`.
4. In the repository, open **Settings → Pages**.
5. Under **Build and deployment**, select **Deploy from a branch**, then **main** and **/(root)**. Save.
6. Open `https://YOUR-USERNAME.github.io/access-decision-lab/` when GitHub Pages publishes the site.

GitHub Pages serves the static files directly. This prototype uses **Tailwind's Play CDN** as requested; Tailwind recommends a precompiled stylesheet for a production application. For a portfolio demo, the zero-build approach is intentional.

## How to walk through it

1. **The decision** — starts with the Friday–Monday case and explains why an existing approval may be reused.
2. **Change one condition** — try `Expired`, `Revoked`, `Per-visit site`, `Permit missing`, `Key not returned`, and the other fixtures. The answer changes immediately.
3. **Three checks** — access authority, operational clearance, and key custody. The gate lines are derived from the actual rule results; there is no arbitrary truncation.
4. **When the answer changes** — the table is evaluated from the same decision function, not hard-coded output labels.
5. **The rule to write down** — copy a reusable rule with definitions, checks, decisions, precedence, acceptance criteria, and assumptions to confirm with a customer.
6. **Acceptance tests** — expand the tests section. All nine fixtures execute in the browser against the same engine as the main answer.

The **Advanced / edit the request** panel allows arbitrary input changes and optional simulated checkout/return events. Those events are local sample data, not a secure audit log. Exporting them produces a local JSON file.

## Decision model

The logic lives in the `evaluateAccess(s)` function within `index.html`, separate from the Vue presentation code.

| Gate | What is checked |
|---|---|
| **Access authority** | Active contractor; not-revoked authorisation; approved dates, area and work; reusability; per-visit site policy |
| **Operational clearance** | Identity verified; protected-area approval if needed; permit-to-work if needed |
| **Key custody** | Previous checkout closed; physical key available |

Outcomes:

- **Eligible** — no new access approval is required; a *new, separately recorded key checkout* may proceed.
- **New approval** — approval expired, scope differs, a consumed single-use approval is offered, or site policy requires a per-visit approval.
- **Hold** — unresolved identity, invalid date input, or missing required work permit/verification.
- **Blocked** — access inactive or revoked, previous key checkout open, or physical key unavailable.

When multiple problems exist, status priority is **Blocked > Hold > New approval > Eligible**, but every failing rule is preserved in the decision result.

### Tests included in the app

| Test | Scenario | Expected |
|---|---|---|
| T01 | Valid repeat visit after Friday return | Eligible |
| T02 | Approval expired before Monday | New approval |
| T03 | Access explicitly revoked | Blocked |
| T04 | Site approves each visit | New approval |
| T05 | Different access area | New approval |
| T06 | Permit-to-work missing | Hold |
| T07 | Previous checkout not closed | Blocked |
| T08 | Protected area owner approval missing | New approval |
| T09 | Single-use authorisation already consumed | New approval |

The engine was also checked independently against additional cases for missing identity, missing contractor, invalid date ranges, unavailable keys, and conflicting failures.

## Important design decisions

- The **answer comes first**, ahead of configuration and technical details.
- The sample request is presented as a **case ticket**, not a wall of switches.
- **Scenario chips** expose counterfactuals immediately; full inputs are hidden in the advanced panel.
- **Returning a key does not change the authorisation**. Issuing a key creates a new custody movement. A single-use visit is only marked consumed when the approval type is single-use.
- The primary artifact is **a rule someone can implement**, not an analytics dashboard.
- UI visual direction is **informed by the concise, high-contrast editorial language and blue portierX product imagery** in public portier material. This is **not an official portier design system**, and this project does not use official portier trademarks or pretend to be their product.

## Limits

This is a **case-study simulation**, not an access-control product. It does not talk to locks, verify human identity, validate real permits, persist records to a tamper-evident audit log, or enforce a customer's actual security policy. Real issue/reissue actions must use verified source records, configured site policies, authorisation from the responsible owners, and a secure auditable backend.

The rule specification is a proposed default model; site-specific policies can override it. In particular, a date-valid approval **does not alone establish permission for another visit**.

## Technical note

- Vue 3 browser build from jsDelivr CDN
- Tailwind Play CDN, with custom CSS tokens for the product-specific visual treatment
- Font Awesome from cdnjs
- Single `index.html`, no compile step, no persistence, no tracking, no AI inference at decision time

The prototype was developed with AI assistance, and the rule decisions remain explicit, inspectable JavaScript rather than generative model outputs.
