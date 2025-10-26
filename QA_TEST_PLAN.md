## Multi‑Order Trading – Business Test Plan & Checklist

### Objective
Protect end‑user funds and reputation by verifying that all trading journeys behave correctly in real-world use, with a special focus on fee safety, order accuracy, and clear user experience.

### Success Criteria
- Users can confidently set up and place Long/Short orders (single or batch) with clear feedback.
- Execution fees are always shown/handled so orders cannot be placed without adequate funds for fees.
- Orders appear correctly in the Active Orders list and positions appear in the Active Positions list.
- Users can easily cancel orders and understand system states (loading, empty, errors) without confusion.

### Scope (Business View)
- In scope: wallet connection, market selection, price display, order set‑up, fee handling, order placement (single/batch), order lists, cancel, positions visibility, alerts/messaging, and resilience to temporary service issues.
- Out of scope: internal implementation details, developer tooling, and infrastructure specifics.

### Risks & Mitigations
- Incorrect or missing execution fees → Block order placement and show a clear message until resolved.
- Wrong direction/market or size due to configuration mistakes → Preview and confirmations must be easy to read and consistent.
- Data delays from external services → Keep previous data visible, avoid UI flicker, show gentle “updating” or “temporarily unavailable” notes.

### Roles
- Business Tester / QA: Runs the scenarios below, records results, raises issues.
- Product Owner: Confirms acceptance criteria and signs off.

### Entry Conditions
- Test wallet with a small balance for fees and test orders.
- App reachable in a browser; wallet tool available (e.g., Rabby/WalletConnect).
 - Use affordable test amounts aligned with budget (avoid high‑value orders in real testing).

### Exit Conditions
- All scenarios below pass without critical issues; any minor items have a mitigation or follow‑up.

---

## Business Scenarios & Checklists

### A) Connect & Prepare
- [ ] Open the app; no blocking error messages.
- [ ] Connect wallet; the address is shown and the app indicates it is ready.
- [ ] Switch between available markets (e.g., ETH/BTC); price updates and labels are correct.

### B) Configure Orders (Preview)
- [ ] Set direction (Long/Short) and confirm the preview updates accordingly.
- [ ] Choose a start method (distance or percentage) and spacing; preview prices should look sensible.
- [ ] Select quick sizes and/or a saved custom set; the number of preview rows and sizes should be as expected.
- [ ] Change leverage; total sizes in preview adjust accordingly.
- [ ] If any required value is missing, the “Place” action remains disabled.
 - [ ] Note affordable sizing for all test rows (stay within testing budget).

### C) Execution Fee Safety
- [ ] Placing an order always shows or internally handles an execution fee; orders cannot proceed if fee funding is insufficient.
- [ ] Try a small order: the app should still require fees and proceed only when covered.
 - [ ] Try an affordable (within‑budget) order and a batch of orders: the system must ensure total fees are accounted for.
 - [ ] Cross‑check on GMX Portal: verify each order’s execution fee matches expectations and that required fees are present before execution.

### D) Place Orders (Single & Batch)
- [ ] Place a single limit order; the app shows a success state and the order appears in Active Orders.
- [ ] Place a batch (3–5) of limit orders; all appear in Active Orders with correct data (market, direction, price, size, leverage, time).
 - [ ] Cross‑check on GMX Portal: confirm the order(s) are visible with the same market/direction, trigger price, and sizing.

### E) Orders List – Readability & Refresh
- [ ] Active Orders list shows recent orders first; no visual flicker during auto‑refresh.
- [ ] If many orders exist, the list paginates and navigation works (next/prev pages).
- [ ] Status labels are clear (e.g., Placed, Cancelled); times look reasonable.
 - [ ] Cross‑check on GMX Portal: the set of open orders matches (counts and key fields).

### F) Cancel Orders
- [ ] “Cancel All” cancels existing orders and updates the list.
- [ ] With no orders, “Cancel All” does nothing harmful and shows no alarming message.
 - [ ] Cross‑check on GMX Portal: cancelled orders are removed/updated accordingly.

### G) Positions Visibility (Read‑Only Safety)
- [ ] Active Positions shows current open positions with key figures (size, collateral, entry, mark price, leverage, est. liquidation, PnL).
- [ ] When no positions exist, a friendly message states there are none.
- [ ] Auto‑refresh updates positions without jumping or flickering; if an update fails briefly, the previous data remains visible.
 - [ ] Cross‑check on GMX Portal: open positions (market, direction, size, entry) match what is displayed.

### H) Long/Short Coverage
- [ ] Long orders and positions behave and display correctly.
- [ ] Short orders and positions behave and display correctly (including liquidation and PnL direction).
 - [ ] Cross‑check on GMX Portal: verify both Long and Short examples reflect accurately.

### I) Custom Settings
- [ ] Saved/custom size sets can be selected and combined with quick sizes.
- [ ] The final preview and placed orders reflect the chosen set exactly.

### J) Messaging & Error Handling
- [ ] If prices or services are temporarily unavailable, the app displays concise, non‑technical messages and recovers automatically.
- [ ] Any action that cannot proceed shows a human‑readable reason (e.g., “insufficient funds for fees”).

---

## Acceptance Criteria
- Order previews always match placed orders (market, direction, prices, sizes, leverage).
- Execution fees are always required and never bypassed.
- Users can cancel orders at any time and see reliable lists of orders and positions.
- The interface remains stable (no flashing or resets) during normal periodic updates.
 - Mandatory cross‑verification: orders, fees, and positions match the GMX Portal at each relevant step.

---

## Zero‑Loss Safety Framework (Business Controls)

### 1) Pre‑Trade Hard Stops (must all pass before “Place” is allowed)
- [ ] Known market, direction, and affordable size(s) are selected and previewed.
- [ ] Execution fee is known, affordable, and below the defined fee ceiling.
- [ ] Available wallet balance covers: total fees + intended order collateral.
- [ ] Price and leverage look reasonable; no negative or extreme values.
- [ ] Daily/session spend cap not exceeded (see Limits below).
- [ ] Mandatory cross‑check: key fields match on GMX Portal preview (market, direction, trigger price, fee).

### 2) Runtime Guards (during placement)
- [ ] If any fee or price changes beyond tolerance between preview and confirm, block placement and require re‑confirmation.
- [ ] If any upstream service becomes unstable (repeated errors), automatically pause placements (“kill switch”) and show a message.
- [ ] Dual‑approval for orders exceeding a defined amount threshold (second human check).

### 3) Post‑Trade Reconciliation (after placement)
- [ ] Compare placed orders in the app vs GMX Portal (count and key values).
- [ ] Record transaction references and timestamps for audit.
- [ ] For batches, verify the entire set is visible and fees match total expectations.
- [ ] If any variance is detected, raise an alert and pause further placements.

### 4) Limits & Budget Management
- [ ] Per‑session budget: stop automatically once total placed size or total fees reach the preset cap.
- [ ] Per‑order maximums: refuse orders above the agreed threshold unless dual‑approved.
- [ ] Daily budget: refuse new placements once daily cap is reached.
- [ ] Affordability rule: use small/affordable amounts for real‑money testing only.

### 5) Canary & Phased Rollout
- [ ] Start with a single affordable order to confirm live behavior (canary).
- [ ] If canary passes checks, allow a small batch; if any check fails, stop.
- [ ] Only after consistent results escalate to normal batch sizes.

### 6) Error & Incident Response
- [ ] On repeated provider errors, enable pause (“kill switch”) and display a simple explanation to the user.
- [ ] Keep last known order/position data visible to avoid confusion.
- [ ] Provide a clear “what to do next” message (retry later, switch network, contact support).

### 7) Change Control & Rollback
- [ ] Any change to fee logic, leverage handling, or order serialization requires a fresh pass of the business scenarios.
- [ ] Keep the previous version deployable (rollback plan) in case of unexpected behavior.
- [ ] Document the version used for live testing in the test evidence.

### 8) Go/No‑Go Checklist (before live operation)
- [ ] All business scenarios passed with affordable amounts.
- [ ] GMX Portal cross‑checks completed for fees, orders, and positions.
- [ ] Budgets and limits configured and validated.
- [ ] Pause/kill‑switch verified (simulate upstream instability and confirm the app pauses placements).
- [ ] Sign‑off by Product Owner and Business Tester/QA.


## Test Evidence
- For each scenario: mark pass/fail, capture a brief note and, if helpful, a screenshot of Active Orders / Active Positions.

## Sign‑off
- Business Tester confirms all scenarios meet expectations.
- Product Owner provides final approval before going live.


