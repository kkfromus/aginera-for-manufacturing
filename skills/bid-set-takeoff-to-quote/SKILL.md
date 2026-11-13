---
name: bid-set-takeoff-to-quote
description: For manufacturers and fabricators who quote from construction drawing sets — structural and miscellaneous steel, rebar, precast, millwork and casework, doors and hardware, windows and curtain wall, ductwork and HVAC equipment, switchgear and lighting. Runs an Aginera takeoff on the customer's bid set, keeps every count and length cited to its sheet, and turns it into RFQ line items for Claude for Manufacturing's customer-inquiry-intake and quote-build. Use when someone says "a bid set came in," "quote this job from the plans," "take off the doors on this set," "how many tons of misc steel," "price this from the drawings," or "what's our scope on these plans."
---

# Bid set → takeoff → quote

Read `../../shared/aginera-basics.md` first. Its cost and consent rules apply
to every step here.

A building-products RFQ is not a part drawing. It is a set of architectural,
structural or MEP sheets, and the manufacturer's scope is one slice of it:
the doors on the door schedule, the embeds and lintels on the structural
sheets, the diffusers on the mechanical plans. The quote depends on getting
that slice's quantities right and showing where each one came from.

## Steps

### 1. Pin down the scope before measuring

From the RFQ email or the user, record:

- **Trade scope**: the product families this plant makes, as the plant names
  them. If the manufacturing plugin's plant setup has a capability list, read
  it; otherwise ask once.
- **Spec sections** named in the RFQ (for example 05 50 00, 08 11 13,
  23 37 13), if any.
- **Exclusions** stated by the customer ("by others", "NIC", "owner
  furnished").
- **Bid due date** and the customer's reference.

If the user gives no scope, ask one question: which products on these plans
are theirs to quote.

### 2. Get the set into Aginera

Follow *Getting the drawing in*. One project per job. Add every sheet the
customer sent, including addenda, as separate documents so revisions stay
traceable.

### 3. Reuse before running

`list_takeoffs` on the project. If a completed takeoff covers the scope, read
it and skip to step 5.

### 4. Run the takeoff (ask first)

State the sheet count, the scope, and the cost Aginera reports, and ask. On a
yes, `run_takeoff` and poll `get_takeoff`. While it runs, draft step 6's
questions from what the RFQ already shows.

### 5. Read and filter to the plant's scope

- `get_takeoff_items` for counts and areas, `get_takeoff_routes` for measured
  lengths, `get_schedules` for door, window, fixture and equipment schedules.
- **Read every page of results, in slices.** Filter each call by `trade`,
  `category`, `item_type` or `page_number`, keep `detail` at `summary` and
  `limit` at 100 or less — a large set returns more than fits in one reply.
  Follow `next_cursor` until `has_more` is false. Ask for quantities in the
  units the plant prices in (`units`).
- **Filter by `item_type` and description, not `category` alone.** A
  category such as `lighting` also holds outlets for light fittings, dimmer
  modules and breakers. Keep only items inside the trade scope. List
  out-of-scope items the takeoff found in a short "seen but not ours" line,
  so the estimator can confirm nothing was dropped by mistake.
- **Never add up the same item across sheets.** One fixture often appears on
  the lighting plan, the reflected ceiling plan and a circuit schedule; the
  description says so with "[also on pN …]". Take one quantity per tag per
  area, say which sheet it came from and which sheets repeat it. Do not use
  the response's `totals` for quote quantities — they sum every row.
- **Hold back what is not a firm quantity.** Rows with quantity 0, low
  confidence, or text such as "existing", "no modification" or "name
  unknown" go to a review list with their sheet, not into quote lines.
- **Schedule vs plan mismatches are findings, not errors.** When the schedule
  says 42 doors of a type and the plans show 40 tags, report both numbers with
  both sheets. That is a question for the customer, not a number to average.
- Group into quote lines the way the plant prices: by type, size, finish,
  gauge or rating — whatever the schedule distinguishes.

### 6. Build the intake

With Claude for Manufacturing installed, load its `customer-inquiry-intake`
SKILL.md and produce its intake form, with:

- One line item per quote line, quantity = `effective_quantity`.
- **For a fabricated assembly** (a skid, a panel, a frame), the line item is
  the assembly the customer buys, one per unit tag. The takeoff rows become
  its bill of materials, kept with their sheets and item ids for
  `quote-build` to cost; they are not separate line items.
- Material, pressure, temperature and size stated on the sheets go into
  `material` and `specs` with `reading_basis: printed_callout` and the sheet.
  When the same item is described differently on two sheets (a tank shown as
  2,800 gal on one sheet and 2,000 gal on three others), that goes in the
  ambiguous register with every value and sheet.
- `field_sources` for every quantity pointing at the Aginera citation:
  document name, sheet number, and the Aginera item id (`itm_…`).
- Schedule-vs-plan mismatches in the **ambiguous** register; scope items the
  set never specifies (finish, hardware set, gauge) in the **missing**
  register.
- The drafted customer questions from those two registers.

Then hand to `quote-build` as that skill directs. Without the manufacturing
plugin, deliver the filtered takeoff as a table (or `create_export`) with
the sheet column kept.

### 7. When the customer answers or revises

New addendum → add it as a new document, rerun only the affected sheets (ask
first), and show what changed by line. Corrections the estimator makes go
back through `review_takeoff_item`.

## Output

1. A one-paragraph scope summary: what was measured, from how many sheets,
   what was excluded.
2. The quote-line table: line, quantity, unit, sheet(s), Aginera item id.
3. Findings: mismatches and missing information, each with its sheets.
4. The intake form handed on, or the export file.

Never present a quantity without its sheet. Never round a measured length
into a different unit without saying so.
