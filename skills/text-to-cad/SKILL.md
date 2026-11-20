---
name: text-to-cad
description: Builds a 3D CAD model of a mechanical part or small assembly with Aginera from a description and its main sizes, or from a PDF part drawing — bracket, flange, shaft, plate, enclosure, fixture, frame — then changes it in plain words or exact dimensions, measures the solid against what was asked, and returns STL/3MF (free) or STEP and a drawing. Gives Claude for Manufacturing's rfq-to-quote and quote-build a measured model (mass, volume, bounding box) when the plant has no Fusion desktop, and runs dimension sweeps for design-space-explore's hand-off shape. Use when someone says "model this part," "make a CAD of a bracket," "text to CAD," "I need a STEP of this," "turn this print into a 3D model," "try it at 4 mm and 6 mm wall," or "we don't have Fusion."
---

# Text to CAD

Read `../../shared/aginera-basics.md` first.

## When this fits

- A customer sent **written specs only** (Claude for Manufacturing's
  `geometry_path: A`), or a **PDF part drawing** (`C2`) and the plant has no
  Fusion desktop connected.
- An engineer wants a quick model to quote from, print a fit check, or send
  to a supplier.
- A few variants of one part (wall thickness, hole count, length) to compare.

It is not a replacement for the engineer's own model in Fusion when one
exists: use that, and Claude for Manufacturing's `design-space-explore`.

## Steps

### 1. Get the requirement straight

Before building, collect: what the part is, material, the main sizes with
units, holes and their positions, and anything that must mate with something
else. When the RFQ intake form exists, take these from it with their
`source_ref`; every size that came from text stays `from_text`.
Ask for a missing main size rather than guessing it. A guessed size is
listed as an assumption.

### 2. Build

- From text: `create_cad_model` with what it is and its main sizes.
- From a PDF drawing: get the PDF into Aginera (basics file) and build from
  its `doc_` id.

Show the user the `viewer_url` straight away.

### 3. Change it

`change_cad_model` once per change, in plain words ("add two 8 mm holes
30 mm apart") or exact values for the named dimensions.
For exact values, try them first with `dry_run: true`: it answers whether
the model would build and gives its size and volume, and changes nothing.
Use dry runs for variant sweeps, then apply only the variant the user picks.

### 4. Check before calling it right

`get_cad_model` measures the solid and lists what was found, what is close
and what is missing against the request. Pass `check_parameters: true` to
learn which named dimensions really drive the solid — a dimension that
changes nothing cannot be swept.
Report each requested size: asked, measured, and found / close / missing.
Never tell the user the model is right while anything is listed close or
missing.

### 5. Files

`get_cad_model_files`:

| File | Cost |
|---|---|
| STL, 3MF | Free |
| STEP, 2D drawing | The model's one-time purchase — ask first; a checkout link is for the user to complete |

## Hand-offs to Claude for Manufacturing

- **rfq-to-quote / quote-build** — pass the model's measured volume, mass
  (volume × the material's density, density cited), bounding box (stock
  size) and feature list, plus the viewer link. Label the basis "Aginera
  text-to-CAD model, measured". From written specs it stays **a ROM only**,
  per Claude for Manufacturing's path-A rule; from a drawing, every size
  keeps its sheet reference.
- **Variant sweeps** — when Fusion is not connected, build one row per
  dry-run variant in the shape `design-space-explore` hands to
  `trade-study-report`: the changed parameters, then mass, volume and
  bounding box. Say they were measured by Aginera, not Fusion, and drop any
  parameter `check_parameters` showed has no effect.
- **Supplier RFQs** — a STEP goes into `supplier-quote-request`'s package
  only at a revision the user confirms.
- **Fusion** — when Fusion desktop is connected later, import the STEP there
  as the starting model.

## Never

- Present a model as matching the drawing while `get_cad_model` lists
  anything close or missing.
- Quote firm from a model built from text alone.
- Buy a STEP or drawing without the user's yes.
