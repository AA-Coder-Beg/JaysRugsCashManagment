# JaysRugsCashManagment

A self-built Excel system for detecting, diagnosing, and resolving cash discrepancies between physical drawer counts and Square Point-of-Sale records.

## Business Problem
Cash handling at the booth relied entirely on informal counting and memory, with no structured process for detecting discrepancies, tracing their root cause, or verifying that recorded cash movements matched physical reality. Without a reconciliation system, errors could go undetected indefinitely, and there was no way to distinguish between counting mistakes, process failures, and genuine cash loss.

This project documents the evolution of cash handling at the booth, from informal manual tracking through two prior workbook iterations, into a system that automatically flags and helps diagnose discrepancies between the Transaction Log and Square POS. It was built after small, unexplained discrepancies started appearing. One notable case was a $20 shortage in the physical drawer versus what Square POS reported, with no clear cause.

## Stakeholder Question
Can we trust that our cash handling process is accurate, and if discrepancies occur, do we have the visibility to identify where and why they happened?

## Evolution
**V0 — Zoho Sheet (Manual, Two-Sheet)**
Cash tracking originally lived in a Zoho Sheet alongside card transactions (run through Square POS for identification), in two sheets: *Cash Register* and *Daily Cash Total*.
- **Cash Register** logged the date, the physical receipt ID from the drawer, amount owed, amount given by the customer, change, a running net cash figure (effectively an early prototype of what later became the Transaction Log's running balance), and the cash float. Receipt ID tracking was eventually dropped after the drawer's counter looped back to 0000 past 9999.
- **Daily Cash Total** was a bare-bones summary: net cash at end of day, float cash, and take-home cash (skim).

This phase worked but was manual and messy, with no built-in way to catch or diagnose discrepancies — it simply recorded what happened.

**V1 — Two-Sheet Excel Workbook**
The system moved to Excel with two sheets: *Daily Summary* and *Denomination Detail*.
- **Daily Summary** compared the starting balance against the expected and actual drawer total, using conditional logic to flag a shortage, overage, or even drawer, and calculate the variance amount.
- **Denomination Detail** added a manual count of float cash and coins ($5s and $1s) at the start of each drawer session, intended as a second source of truth against Square POS.

Denomination Detail's heavy manual entry made it error-prone. In one instance, a miscounted dime created a 10-cent overage. Because earlier entries couldn't be edited after the fact, that overage stayed in the sheet permanently — but it never reached Daily Summary, since reconciliation was already running off Square POS data independently. That incident confirmed Denomination Detail was adding manual risk without adding accuracy, and it was retired.

**V2 — Current System**
The current workbook contains three sheets: *Daily Summary*, *Transaction Log*, and *Journal Entries*.
- **Transaction Log** records each individual transaction — whether cash moved in or out, the time it occurred, and the item sold for cash sales — and replaced Denomination Detail as the second source of truth against Square POS.
- **Journal Entries** capture the narrative context behind each transaction, making it possible to trace a flagged variance back to a specific event rather than just a number.

## Tools & Methods
- **Excel** — Functional logic using `LET()` and `IF()` to automate variance detection and classify drawer status (short / over / balanced)
- **Two-source reconciliation** — cross-referencing the Transaction Log against Square POS reporting rather than relying on a single count
- **Root cause tracing** — Journal Entries sheet used to narrow discrepancies down to a category (e.g., counting error vs. process gap) rather than just flagging that one exists

## Results

| Metric | Before | After |
|---|---|---|
| Reconciliation time | ~10 min (up to ~20 min when diagnosing a discrepancy) | ~5 min |
| Discrepancy frequency | ~1–2 per 2 weeks | Rare; any that occur are caught and resolved same-day |
| Root cause visibility | None — discrepancies were unexplained | Narrowed to category (e.g., the original $20 shortage was traced to a counting error, not a process or data gap) |

These are operational estimates based on day-to-day use rather than formally logged time-tracking data.

## Lessons Learned

The biggest improvement in this project didn't come from adding more verification steps — it came from removing one. Denomination Detail was a reasonable idea on paper, but in practice its manual entry introduced more risk than it caught, while the Transaction Log already provided accurate, lower-effort reconciliation against Square POS. Any future addition to this system needs to justify its time cost the same way Denomination Detail ultimately couldn't.

## Link to live dashboard
https://public.tableau.com/views/CashManagementAnalysis2025-2026/SessionReconciliationOverview?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link
