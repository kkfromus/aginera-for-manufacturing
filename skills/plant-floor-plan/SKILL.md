---
name: plant-floor-plan
description: Turns a plant's floor plan or layout drawing into clean CAD and visuals with Aginera — a DXF/DWG of the layout, measured areas, and 3D images or a walkthrough film of the floor for customer visits, hiring, safety induction or expansion planning. Gives Claude for Manufacturing's daily-production-report a cleanly labelled layout to draw its floor view from. Use when someone says "set up our floor plan," "the floor view can't read our layout," "make a 3D view of the plant," "a walkthrough video of the shop floor," "convert our plant layout to CAD," or "how many square feet is each cell."
---

# Plant floor plan → CAD, measurements and 3D

Read `../../shared/aginera-basics.md` first.

## What the user can get

| Want | Aginera tools | Cost |
|---|---|---|
| A clean CAD layout (DXF/DWG) | `convert_pdf_to_cad`, `get_cad_file` | Check and view free; download paid on free accounts |
| Areas per cell, zone or bay | `run_takeoff` → `get_takeoff_items` | Credits |
| 3D images and a walkthrough film | `render_floor_plan` → `get_floor_plan_render`; add-ons via `get_floor_plan_renders` / `create_render_checkout` | Credits / paid add-ons |

Ask which of these the user wants. Run only those.

## Steps

1. **Get the layout in** — *Getting the drawing in*. One project per site,
   named for the site. Note which floor the drawing shows; one floor per
   document.

2. **For the production report's floor view.** Claude for Manufacturing's
   `daily-production-report` reads the plan from a PDF or image, and stops
   when labels are too small or rotated to read. When that happens, or before
   it does:
   - Run the free `convert_pdf_to_cad` check and view the result with
     `get_cad_file`.
   - The CAD's text entities give each machine label verbatim, with its
     position. Use them to prepare the label list (label, area/zone, rough
     position), marking any label the conversion did not produce as unread.
   - Then load `daily-production-report`'s floor-schematic reference and run
     its setup turn with the original plan plus this label list. Its rules
     still govern: the user confirms every machine match before anything is
     saved, and unread labels are asked about, never guessed.

3. **Areas** — on request and after a yes, run a takeoff and report area per
   zone, cell or bay with its sheet. Useful for cost allocation, layout
   changes and expansion budgets.

4. **3D images and walkthrough film** — on request:
   - The renderer reads rooms, walls, doors and windows. It works best on
     office, amenity and building areas; an open shop floor drawn as machine
     outlines may come back `no_renders`. Say so before starting.
   - State what Aginera will produce, the 5–15 minute wait, and the cost.
     Pass `pages` with the floor-plan sheets — on a multi-sheet set the
     whole-document run can return `no_renders` when the same sheets render
     fine on their own. Use `get_document` to find the sheets. On a yes,
     `render_floor_plan`, then poll `get_floor_plan_render`.
   - On `no_renders`, report the message, retry once with `pages` if it was
     omitted, and otherwise ask for a plan-only PDF.
   - Return the image and film links.
   - Paid add-ons (more images, films, remixes) are listed by
     `get_floor_plan_renders`. Offer them only if the user asks for more;
     `create_render_checkout` returns a link the user completes themselves.
   - Say plainly that renders are visualisations from the 2D plan: equipment
     shapes and finishes are representative, not as-built. Do not use them
     for clearance, safety-distance or code checks.

## Output

The pieces requested: the CAD file, the label list handed to the floor-view
setup, the area table with sheets, and/or the render links — each with what
it can and cannot be used for.
