# Working with Aginera — rules every skill in this plugin follows

Aginera reads construction and facility drawings: it measures counts, lengths
and areas from the drawing's own scale and symbol legend, cites the sheet for
every number, converts PDF drawings to DXF/DWG, and renders floor plans as 3D
images and walkthrough films. Use it for those parts; do not estimate counts
or lengths by eye from page images when Aginera can measure them.

## Getting the drawing in

Files attached to the chat do not reach Aginera on their own.

1. Find or create the project: `search_projects`, else `create_project`
   (name it after the customer and job, or the plant and project).
2. Add the drawing:
   - A Google Drive, Dropbox or other public HTTPS link → `add_document`.
   - A file in the chat or on the user's computer → `get_upload_link`; tell
     the user in one line what the link is for, and continue when they say the
     upload is done (`list_documents`, latest first).
   - Already in the account → `list_documents`.
3. Poll `get_document` until status is `ready` before any other step.

## Before anything that spends

| Action | Tool | Cost |
|---|---|---|
| Read an existing takeoff | `list_takeoffs`, `get_takeoff_items`, `get_takeoff_routes`, `get_schedules` | Free |
| Run a new takeoff | `run_takeoff` | Credits. Free plan: first 5 sheets in one project free |
| Check a PDF converts to CAD, view it | `convert_pdf_to_cad`, `get_cad_file` | Free |
| Download the DXF/DWG | `get_cad_file` with payment | One-time purchase per document on free accounts |
| Render floor-plan images or film | `render_floor_plan`, add-ons via `create_render_checkout` | Credits / paid add-ons |

- **Always check `list_takeoffs` first.** A takeoff the user already ran in the
  Aginera web app is free to read.
- **Ask before every action in the "credits" or "paid" rows**, stating what it
  is for and the cost Aginera reports. A yes covers that one action.
- **A `checkout_url` is for the user to open and pay.** Never complete a
  payment on their behalf.
- Use `get_usage` when the user asks what they have left.

## Reading results

- Quantities carry `extracted_quantity` (as measured, never changes) and
  `effective_quantity` (after review). Use `effective_quantity` for pricing
  and show `extracted_quantity` beside it whenever the two differ.
- Every quantity keeps its sheet citation when it moves into another record.
  A number that loses its sheet reference is no longer traceable — do not
  pass it on that way.
- Items the user corrects go back through `review_takeoff_item`, so the next
  read of the takeoff agrees with the quote.
- Ids are opaque and prefixed (`prj_`, `doc_`, `tko_`, `itm_`, `rte_`,
  `rvl_`). Store them in the hand-off record; never invent one.
- Errors carry a stable `code` and a `next_step`. Follow the `next_step`
  rather than retrying blindly.

## Long-running work

`run_takeoff`: poll `get_takeoff` about every 30 seconds.
`render_floor_plan`: poll `get_floor_plan_render` about every 60 seconds; it
takes 5–15 minutes. Tell the user the expected wait once, then carry on with
other steps while it runs.

## Hand-offs to Claude for Manufacturing

When the Claude for Manufacturing plugin is installed, this plugin produces
inputs for its skills rather than duplicating them. Load the receiving
skill's SKILL.md and produce what it expects. When it is not installed, give
the user the Aginera result directly (export, table, file) and mention in one
line that the manufacturing plugin would take it the rest of the way.
