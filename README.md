# Aginera for Manufacturing

Construction-drawing intelligence for manufacturers, fabricators and plant
teams, built to work alongside Anthropic's **Claude for Manufacturing**
plugin.

Many manufacturers quote from building drawings, not part prints: steel
fabricators, precast and rebar shops, millwork and casework, doors and
hardware, windows and curtain wall, HVAC equipment and ductwork, switchgear
and lighting. Their estimators spend hours counting and measuring bid sets by
hand. This plugin connects Claude to Aginera, which measures the drawings
using their own scale and legends and cites the sheet for every quantity,
then hands the results to Claude for Manufacturing's quoting and reporting
skills.

## Skills

| Skill | What it does |
|---|---|
| `bid-set-takeoff-to-quote` | Runs a takeoff on a customer's bid set, filters it to the plant's product scope, flags schedule-vs-plan mismatches, and builds the RFQ intake form for `customer-inquiry-intake` and `quote-build`, with every quantity traced to its sheet. |
| `drawing-to-cad` | Converts a PDF drawing to DXF/DWG, checks the scale against a known dimension, and brings it into Fusion as a sketch for the `rfq-to-quote` design step. |
| `plant-floor-plan` | Turns a plant layout into clean CAD, area measurements and 3D images or a walkthrough film, and gives the `daily-production-report` floor view a cleanly read machine-label list. |
| `plant-project-takeoff` | Measures quantities for the plant's own expansions and build-outs for budgets, comparable contractor RFQs via `supplier-quote-request`, and checking contractor bids. |

## Connector

The plugin uses the Aginera MCP server at `https://mcp.aginera.ai/mcp`.
You sign in to your Aginera account the first time Claude uses it. No API
keys are stored in the plugin.

## Costs and consent

Reading existing takeoffs, checking whether a PDF converts, and previewing
CAD are free. Running a new takeoff, downloading CAD files and rendering
images or films use Aginera credits or a one-time purchase. Claude asks
before every action that spends, and any checkout link is for you to
complete. Claude never pays on your behalf. New Aginera accounts get their
first 5 sheets in one project free.

## Works best with

- **Claude for Manufacturing** (Anthropic): intake, quoting, supplier RFQs and
  production reporting. Without it, the skills still deliver Aginera's
  takeoffs, exports, CAD files and renders directly.
- **Autodesk Fusion**: connected through Claude for Manufacturing, for
  importing converted drawings.

## Data handling

Drawings you add are stored in your Aginera account and processed by Aginera
to produce takeoffs, CAD files and renders. The plugin sends data only to the
Aginera connector declared above. Privacy policy: https://aginera.ai/privacy

## Support

support@aginera.ai · https://aginera.ai
