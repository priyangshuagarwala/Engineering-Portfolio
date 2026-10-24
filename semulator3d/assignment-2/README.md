# Assignment 2: Submitted layout artifacts

## Included files

`layouts/A2_Practice.cat` and `layouts/A2_Inverter.cat` are native SEMulator3D Layout Editor files copied unchanged from the submitted Assignment 2 archive. These are coursework layout artifacts based on instructional exercises; they do not claim the underlying teaching ideas as original inventions.

No vendor executable, licensed material database, or course-provided `Pattern.cat` is included. These files require a compatible, separately licensed application; the software itself is not provided. No native opening or compatibility test was performed.

## Practice cell

- Declared units: nanometres.
- Four rectangle commands: one boundary and three pattern features.
- Stored total extent: x = -33 to 19; y = -15.5 to 16.5.
- Layer-index/name consistency needs review: the boundary rectangle references `layer1` at index 0, while the library declares index 0 as `Boundary`. This was left unchanged.

## Inverter cell

- Declared units: nanometres.
- Stored total extent: x = 0 to 2200; y = 0 to 4200.
- Geometry layers: PWell, Active, Poly, Nplus, Pplus, Contact, Metal1. An additional `layer1` declaration has no rectangle in the cell.
- 31 rectangle entries, of which four repeat earlier rectangles exactly (two Active and two Contact entries). There are therefore 27 distinct rectangle commands.
- A PWell region contains the lower transistor geometry. Active, implant, poly, contact, and metal rectangles define the intended complementary-device layout and routing.

The submitted screenshot shows the multilayer drawing, but a drawing alone does not verify intended connectivity. There is no reviewed DRC or LVS report, simulation testbench, extracted netlist, or fabrication data supporting sign-off claims.

## Suggested next validation steps

Open a copy in the licensed editor, confirm units and layer mapping, check duplicate geometry, label intended supply/input/output nets, then perform applicable geometric and connectivity verification. These steps are pending, not represented as completed work.
