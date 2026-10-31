# Access Decision Lab

An interactive product case study about **physical access decisions, key reissue, and testable business rules**.

**[Open the live application](https://aliftan.github.io/access-decision-lab/)**

## The case

A contractor is authorised to enter a substation until 31 December. He returns the key on Friday and asks for it again on Monday. Does he need another approval?

**Not automatically.** A key return closes the previous custody record; it does not, by itself, revoke the access approval. The key can be issued again without a new access approval only if the original authorisation still covers the person, date, location, and work; site policy permits another visit; required clearances are valid; and the previous checkout has been closed. The next handover must be recorded separately.

The app turns that answer into a decision model that can be examined, challenged, and translated into requirements for engineering.

## Explore the app

The default case starts with a valid repeat visit. Select a scenario such as **Expired**, **Revoked**, **Per-visit site**, or **Permit missing** to see how the decision changes. Each result shows what action is required and which checks pass or fail.

The experience is organised around three checks:

| Check | Question |
| --- | --- |
| **Access authority** | Is this person authorised for the requested site, area, work, and date? Can the approval be reused? |
| **Operational clearance** | Has identity been verified, and are any protected-area approvals or work permits in place? |
| **Key custody** | Is the previous checkout closed, and is the physical key available? |

The **When the answer changes** section compares alternative outcomes. **Copy the rule** copies `KEY-REISSUE-01`, a proposed engineering specification with decision rules, precedence, acceptance criteria, and assumptions to confirm with the customer.

For a closer look, expand **Advanced / edit the request** to change the inputs, simulate a key issue or return, and export sample activity history as JSON. The activity history is held in browser memory during the session; it is not a persistent audit log.

## Decision outcomes

| Outcome | Meaning |
| --- | --- |
| **Eligible** | The existing approval can be reused under the entered conditions. Record a separate key checkout. |
| **New approval** | The approval has expired, the scope has changed, a single-use approval is consumed, or a site-specific approval is required. |
| **Hold** | A prerequisite such as identity verification or a required permit is unresolved. Do not release the key yet. |
| **Blocked** | Access is inactive or revoked, the previous checkout is still open, or the key is unavailable. |

When conditions conflict, the displayed outcome follows **Blocked > Hold > New approval > Eligible**. The engine retains all failed checks so the highest-priority outcome does not hide other problems.

## Acceptance tests

The app runs these nine fixtures against the same decision engine used by the interactive case:

| ID | Scenario | Expected result |
| --- | --- | --- |
| T01 | Valid repeat visit, key returned | Eligible |
| T02 | Approval expired | New approval |
| T03 | Access revoked | Blocked |
| T04 | Site requires approval for each visit | New approval |
| T05 | Different access area | New approval |
| T06 | Required work permit missing | Hold |
| T07 | Previous checkout still open | Blocked |
| T08 | Protected-area approval missing | New approval |
| T09 | Single-use approval already used | New approval |

You can view the results in the **Acceptance tests** section of the page.

## Implementation

The application is intentionally lightweight and can be hosted as static files on GitHub Pages.

- **`index.html`** contains the interface, sample scenarios, deterministic `evaluateAccess()` rules engine, acceptance fixtures, and browser interactions.
- **`README.md`** documents the problem, decisions, and how to use the prototype.

It uses **Vue 3**, **Tailwind CSS**, and **Font Awesome** via CDNs, with custom CSS for the interface. There is no backend, build step, database, API key, or AI model involved in runtime access decisions. An internet connection is needed to load the CDN dependencies.

The prototype was developed with AI assistance, while the rules themselves remain explicit JavaScript that can be inspected and tested.

### Run locally

Open `index.html` in a modern browser with an internet connection. No installation or build command is required.

### Update the GitHub Pages site

Replace `index.html` and `README.md` in the root of the `access-decision-lab` repository and commit the changes to the published branch. GitHub Pages serves the updated static files.

## Scope and limitations

Access Decision Lab is an **independent concept prototype**, not a portier product or a production access-control system. All people, approvals, keys, and activities shown are simulated. The app does not authenticate users, validate actual permits, connect to locks or access-control hardware, or create a secure, tamper-evident audit trail.

Its decision rules are proposed defaults for discussing product behaviour. A real deployment would need verified records, site-specific policies, authorised decision-makers, and secure enforcement. In particular, an approval's expiry date alone is never enough to authorise another visit.
