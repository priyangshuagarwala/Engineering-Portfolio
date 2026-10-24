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

## What this work demonstrates

- Connecting mask/layout information with process-model inputs and inspecting 3D geometry.
- Reading a small process-parameter experiment and separating geometric measurements from rule-check outcomes.
- Building and organizing multilayer layout geometry for a CMOS inverter exercise.
- Reporting validation limits rather than treating rendered structures as electrical or fabrication proof.

## Provenance, reuse, and validation limits

The user supplied these archives as coursework they performed. Included layout files are submitted project artifacts, preserved byte-for-byte with their original authorship metadata. This does not claim ownership of the software or the supplied teaching examples. Known course-supplied `Pattern.cat`, tutorial archives, instructional PDFs, material databases, and vendor examples are excluded. No license for third-party material is granted by this portfolio.

Review was static: archive inventory, text and native-container inspection, result reading, and screenshot inspection. SEMulator3D was not executed and the models were not independently rerun. Screenshots and animation remain outside this shareable draft because several expose local filesystem paths, and tutorial-derived visuals also need a sharing/provenance check. No NSFET files are included.
