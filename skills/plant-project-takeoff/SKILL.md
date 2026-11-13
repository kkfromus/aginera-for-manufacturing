---
name: plant-project-takeoff
description: Measures the quantities for a plant's own capital project — a line expansion, new building, mezzanine, cleanroom, utility run or electrical upgrade — from its construction drawings with Aginera, then builds a budget-ready quantity list and, with Claude for Manufacturing, a supplier RFQ package with a comparable response format. Use when someone says "we're expanding the plant," "budget this expansion from the drawings," "how much conduit/duct/pipe is on this set," "get quotes from contractors for this build-out," or "check the contractor's quantities."
---

# Plant project takeoff → budget and supplier RFQs

Read `../../shared/aginera-basics.md` first.

Here the plant is the buyer, not the seller. The goal is an independent
quantity baseline: to budget the project, to send comparable RFQs, and to
check the quantities a contractor bids.

## Steps

1. **Scope** — ask which trades matter (electrical, mechanical, plumbing,
   structural, architectural, civil) and whether the purpose is a budget,
   an RFQ, or checking a bid already received.
2. **Get the set in** — *Getting the drawing in*. One project per capital
   project; addenda as separate documents.
3. **Reuse, then run** — `list_takeoffs`; if none covers the trades, ask and
   `run_takeoff`.
4. **Read** — `get_takeoff_items`, `get_takeoff_routes` (conduit, cable
   tray, duct, pipe runs with measured lengths), `get_schedules` (panels,
   equipment, fixtures). Group by trade, then by system.
5. **Deliver for the purpose asked:**
   - **Budget**: the quantity list by trade with sheets. Quantities only —
     apply unit rates only from the plant's own cost history or rates the
     user supplies, each labelled with its source. Never supply a market rate
     from general knowledge as if it were a quote.
   - **Supplier / contractor RFQ**: with Claude for Manufacturing installed,
     load `supplier-quote-request` and build its package with these
     quantities as the demand picture, each line carrying its sheet, so every
     bidder prices the same quantities. Drafts go to the buyer to send.
   - **Bid check**: put the contractor's quantities beside Aginera's per
     line. Report differences above 5% (or the user's threshold) with both
     numbers and the sheets. A difference is a question for the contractor,
     not proof of an error on either side.
6. **Export** — `create_export` for the quantity list when the user wants a
   spreadsheet.

## Never

- Present a takeoff as a contractor's price.
- Drop the sheet reference from any quantity that goes into an RFQ.
