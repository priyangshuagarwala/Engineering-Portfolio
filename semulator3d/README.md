# SEMulator3D: Educational Process and Layout Modeling

A coursework portfolio covering shallow trench isolation (STI), a back-end-of-line (BEOL) training flow, process-parameter experiments, and introductory CMOS inverter layout. The submitted Assignment 1 and Assignment 2 archives support the descriptions below. These are educational models, not fabricated devices or a production-qualified process.

## Assignment 1: STI and BEOL process-modeling tutorials

The first assignment introduces the SEMulator3D workflow: connecting a layout and material database to a process sequence, running a model, and inspecting the resulting three-dimensional structure. Its first module uses an STI training example. Submitted images show material-separated structures and cross-sectional views, providing evidence of model visualization rather than proof of a new isolation process.

The second module uses a supplied BEOL example. The archive contains the process definition, analysis settings, model snapshots, and an experiment sweep. The sweep varies lithography dimensional bias and the trench-etch lateral ratio across four combinations. It records via-bottom critical dimension, via contact area, minimum line-width checks, minimum-insulator checks, and net-count measurements. This provides a concrete example of using process variation to study geometric consequences.

**Important result:** all four recorded runs have `FAILED` status for both `MIN_LW` and `MIN_Insulator`. Their numerical measurements must not be presented as passing design rules. The four runs report a measured net count of two. A completed run and a valid design are different outcomes.

See [Assignment 1 results and interpretation](assignment-1/RESULTS.md). The supplied tutorial source, material databases, manuals, generated script, and bulky intermediate wafer files are not redistributed here.

## Assignment 2: Layout practice and a CMOS inverter

The layout exercise moves from simple mask geometry to a multilayer inverter drawing. Two submitted native layout files are included:

- [`A2_Practice.cat`](assignment-2/layouts/A2_Practice.cat): a four-rectangle practice cell consisting of one boundary rectangle and three pattern features. It exercises geometry creation and layer assignment. Its declared units are nanometres. One rectangle refers to `layer1` at index 0 while the library calls that layer `Boundary`; native-editor checking is still needed.
- [`A2_Inverter.cat`](assignment-2/layouts/A2_Inverter.cat): a 31-rectangle cell with PWell, Active, Poly, Nplus, Pplus, Contact, and Metal1 geometry. It arranges the two transistor regions, a shared gate path, contacts, power routing, and an output-side interconnect. The stored overall extent is 2,200 by 4,200 in the file's declared nanometre units.

The inverter is a **layout exercise**, not an electrically verified standard cell. The archive does not establish DRC, LVS, extracted connectivity, switching behavior, or fabrication readiness. Four rectangle entries duplicate earlier geometry; this is recorded as a review finding, not silently repaired. See [layout notes](assignment-2/README.md).

## Related later coursework: metallization and damascene

Earlier project notes report completion of conventional-metallization and damascene reference runs and saving a process file. They also describe diffusion visualization and checking whether material inputs support a proposed comparison. Those later notes are separate from the newly reviewed Assignment 1 and Assignment 2 archives. The relevant later-assignment sources were not validated in this review; the comparative study and final report remain in progress. FinFET practical work is not presented as completed.

## Process theory behind the models

The following concepts explain how to interpret the coursework. They are general process principles, not additional results from these assignments.

### STI: separating neighboring active regions

Shallow trench isolation places insulating material in etched trenches between active silicon regions. A simplified sequence is **define the trench → etch → form a liner → fill with oxide → planarize**. The liner and fill are distinct process steps; a filled trench must also leave a suitable surface for later fabrication. A cross-section is therefore useful for examining both the isolation region and its boundary with active silicon.

**Chemical-mechanical planarization (CMP)** removes excess material while reducing surface-height differences. The process must clear unwanted overburden without excessive removal of the stop layer or isolation material. Pattern density and feature size can affect polishing, so a locally attractive cross-section does not establish uniform planarization across a layout. This is why material thickness, residual topography and removal margins matter alongside the trench's nominal shape. See the Applied Materials/MIT paper, [Planarization and Integration of Shallow Trench Isolation](https://boning.mit.edu/wp-content/uploads/2022/11/Planarization-and-Integration-of-Shallow-Trench-Isolation.pdf).

### Lithography and etch: from mask shapes to three-dimensional features

A mask specifies an intended pattern; lithography defines the patterned opening, and etching transfers it into the underlying material. Etch removes selected materials and determines a feature's depth and profile. Conductor and dielectric etches serve different parts of the fabrication flow. [Lam Research's etch overview](https://www.lamresearch.com/products/our-processes/etch/) explains this distinction.

For interpreting this assignment, **dimensional bias** represents a modeled change from the intended lateral size, whereas a **lateral-etch parameter** controls a different part of profile evolution. They should not be treated as interchangeable knobs. An opening's top width alone cannot describe its bottom width, sidewall shape or eventual contact area. The sign convention and parameter definition must come from the selected process step rather than from the parameter name alone.

### Metallization: subtractive versus damascene

In a subtractive metal flow, a deposited metal layer is patterned and unwanted metal is etched away. In a damascene flow, openings are first made in a dielectric, then filled with metal; excess metal is removed to leave embedded wiring. Dual damascene combines a via and a wiring trench in an integrated fill scheme. A **line** routes a signal laterally, while a **via** connects wiring levels vertically.

This ordering changes what must be controlled: metal-etch profiles in one approach, and dielectric openings, fill and planarization in the other. Metal, barrier/liner and dielectric occupy the available cross-section together, so the drawn opening is not automatically the electrically conducting area. These distinctions provide context for the coursework; no advanced integration scheme is claimed here. See [imec's explanation of subtractive and damascene integration](https://www.imec-int.com/en/articles/semi-damascene-metallization-inflection-point-back-end-line-processing).

### Variation and verification: asking the right question

A parameter sweep asks how a measured output changes when selected inputs change. For the supplied four-run experiment, compare via-bottom dimension and contact area alongside the line-width and insulation checks. A larger contact area is not an improvement if another required constraint fails. Two varied inputs can also interact, so changing one parameter in isolation may not explain every result.

Keep three questions separate:

1. **Geometry:** did the modeled sequence produce the intended shapes and material separation?
2. **Connectivity and rules:** are the intended nets connected, and are the specified checks satisfied?
3. **Electrical behavior:** does an appropriately extracted or simulated circuit meet its operating requirements?

The recorded experiment supports a limited geometric comparison. Four parameter combinations do not establish a manufacturing distribution, yield prediction or complete process window. Those conclusions would require justified parameter ranges, calibrated models, appropriate sampling and separate validation.

## What this work demonstrates

- Connecting mask/layout information with process-model inputs and inspecting 3D geometry.
- Reading a small process-parameter experiment and separating geometric measurements from rule-check outcomes.
- Building and organizing multilayer layout geometry for a CMOS inverter exercise.
- Reporting validation limits rather than treating rendered structures as electrical or fabrication proof.

## Provenance, reuse, and validation limits

The user supplied these archives as coursework they performed. Included layout files are submitted project artifacts, preserved byte-for-byte with their original authorship metadata. This does not claim ownership of the software or the supplied teaching examples. Known course-supplied `Pattern.cat`, tutorial archives, instructional PDFs, material databases, and vendor examples are excluded. No license for third-party material is granted by this portfolio.

Review was static: archive inventory, text and native-container inspection, result reading, and screenshot inspection. SEMulator3D was not executed and the models were not independently rerun. Screenshots and animation remain outside this shareable draft because several expose local filesystem paths, and tutorial-derived visuals also need a sharing/provenance check. No NSFET files are included.
