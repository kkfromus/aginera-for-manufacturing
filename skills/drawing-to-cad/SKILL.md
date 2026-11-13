---
name: drawing-to-cad
description: Converts a customer's PDF drawing to DXF or DWG with Aginera so it can be imported into Autodesk Fusion or another CAD system — for building an estimating model, checking geometry, or programming a cut. Checks convertibility and price for free before anything is bought. Feeds Claude for Manufacturing's rfq-to-quote design step when the only drawing is a PDF. Use when someone says "convert this PDF to DXF," "I need this drawing in CAD," "get this into Fusion," "we only have a PDF of the print," or "make a DWG from this."
---

# PDF drawing → CAD → Fusion

Read `../../shared/aginera-basics.md` first.

## When this fits, and when it does not

Aginera's conversion is built for architectural, structural, MEP and
facility drawings: walls, grids, plans, elevations, layouts, flat-pattern
and plate outlines. It works well when the geometry is mostly lines, arcs
and text.

Machined-part prints with dense GD&T, many section views and tight
tolerance callouts are a weaker fit. The geometry may convert, but feature
control frames and datum references come across as text or lines, not as
CAD semantics. For those, say so, and treat the DXF as a sketch underlay to
trace in Fusion, never as the authoritative model. The PDF stays the
controlling document either way.

## Steps

1. **Get the PDF in** — *Getting the drawing in*. Confirm the drawing's
   revision letter from the title block and record it.
2. **Free check** — `convert_pdf_to_cad`. Report: convertible or not, which
   pages, the format options, and the price for the download. If it is not
   convertible, say why (from the error's `code`) and stop; suggest asking
   the customer for the native CAD or a STEP file.
3. **Preview** — `get_cad_file` without paying returns the file or its
   price. The download link is time-limited and may not be reachable from
   Claude's own workspace; when it is not, give the user the link and run
   the checks below in Fusion after import, or ask the user to measure the
   one dimension in their CAD tool. Check three things and report each:
   - **Scale**: one known dimension from the drawing measured in the
     converted file. State both numbers. If they differ, the scale is wrong
     and the file must not be used for estimating.
   - **Completeness**: every view on the sheet is present.
   - **Text**: dimensions and notes came across legibly.
4. **Download (ask first when it costs)** — state the price and format (DXF
   for Fusion sketches; DWG if the plant's CAD standard wants it). When
   `convert_pdf_to_cad` reports no charge for this account, say so and
   continue. Otherwise ask; on a yes, use the account's credits, or give the
   user the `checkout_url` to pay. Never pay for them.
5. **Into Fusion** — when the Fusion connector from Claude for Manufacturing
   is connected and the user wants it, import the DXF as a sketch on a new
   design. Never overwrite an existing design. Then follow
   `rfq-to-quote`'s design step for the estimating model, recording the
   geometry path as `C2` (drawing) with the note "from Aginera DXF of
   <file>, rev <x>, scale checked: <dimension>."
6. **Hand back** — the CAD file to the user (and saved beside the PDF when a
   folder is connected), plus the scale check result.

## Never

- Use a converted file whose scale check failed.
- Treat the DXF as the drawing of record. The customer's PDF at its stated
  revision is the record.
- Buy the download without a yes for that document.
