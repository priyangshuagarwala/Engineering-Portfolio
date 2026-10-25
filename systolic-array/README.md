# 8×8 Systolic-Array Convolution Accelerator

A Verilog VLSI design project exploring how a small two-channel convolution can be mapped onto a regular array of multiply–accumulate processing elements.

> Documentation draft based on the public source repository. Authorship/contribution wording and permission to republish source files are awaiting confirmation. No simulation or physical-design run was performed for this review.

## Demo configuration

| Property | Configuration |
|---|---|
| Input tensor | One sample, two channels, 3×5 values per channel |
| Convolution | 2×2 kernel, stride 1, no padding |
| Window size | Eight values across both channels |
| Output positions | 2×4 = eight per kernel |
| Array | 8×8 processing elements |
| Default arithmetic | Signed 8-bit inputs and weights; 16-bit partial sums/outputs |
| Active kernels | Two hardcoded kernels; remaining columns receive zero weights |

The current top module contains its tensor and kernel values in lookup functions. It is a fixed demonstration rather than an externally programmable accelerator. Increasing `ACTIVE_KERNELS` alone does not supply additional kernels: the lookup defines only columns 0 and 1. [Top-level RTL](https://github.com/abhineeth007/systolic-array-8x8/blob/main/Codes/Conv2Ch2x2_SystolicArray_Top.v)

## Architecture

```text
Tensor lookup → im2col address sequence → serial capture and row skew
                                                ↓
                                 8×8 weight-stationary array
                                                ↓
                                  column deskew → aligned outputs
```

The address sequence presents each convolution window as eight scalar inputs. Serial-to-parallel capture and skew prepare these inputs for the array. [Original project explanation](https://github.com/abhineeth007/systolic-array-8x8)

Each processing element first loads a weight, then holds it during computation. On a clock edge it forwards the input data and produces the incoming partial sum plus the signed input–weight product. Reset is asynchronous and active high. [Processing element](https://github.com/abhineeth007/systolic-array-8x8/blob/main/Codes/Processing_Element.v)

Nested generate loops instantiate 64 processing elements. Data advances horizontally; weights load vertically and partial sums propagate downward. Outputs come from the bottom row. The array instantiates `Deskew_8x8` to provide aligned outputs and an associated valid signal. These are RTL structure observations, not measured throughput claims. [Array RTL](https://github.com/abhineeth007/systolic-array-8x8/blob/main/Codes/Systolic_Array_8x8.v)

## Understanding the convolution mapping

For each output location, a convolution combines a small patch from every input channel with the corresponding kernel weights. With two channels and a 2×2 kernel, one output is an eight-term dot product:

**output = Σ(input-window element × corresponding kernel weight)**

Moving the window one position to the right creates the next dot product. A 3×5 image with a 2×2 window and unit stride has two legal vertical positions and four horizontal positions. Therefore each kernel produces eight outputs. This is the mathematical interpretation of the configured demonstration, not a measured hardware result.

### Worked first-window example

Using the tensor and weights defined in the top-level lookup functions, flatten the top-left window channel by channel:

- Channel 0 contributes `1, 2, 6, 7`.
- Channel 1 contributes `−1, −2, −6, −7`.
- The combined window is `x = [1, 2, 6, 7, −1, −2, −6, −7]`.

For kernel 0, `w = [1, 0, 0, −1, 1, 1, 0, 0]`:

**x · w = 1 + 0 + 0 − 7 − 1 − 2 + 0 + 0 = −9**

For kernel 1, `w = [0, 1, 1, 0, −1, 0, 0, 1]`:

**x · w = 0 + 2 + 6 + 0 + 1 + 0 + 0 − 7 = 2**

These independently calculated scalar examples explain the first documented outputs; they do not validate cycle timing, reset behavior or the complete RTL. [Input and kernel definitions](https://github.com/abhineeth007/systolic-array-8x8/blob/main/Codes/Conv2Ch2x2_SystolicArray_Top.v)

### Why im2col, skew and deskew are useful

Conceptually, `im2col` rearranges overlapping image windows into a matrix suitable for dot products. Here, eight windows of eight elements give an 8×8 collection of values. Address generation can traverse the original tensor in window order rather than requiring a separately stored, fully expanded matrix.

A systolic array moves values between neighboring processing elements over successive clocks. Skew staggers input lanes so each activation meets its intended partial sum. Different output columns naturally encounter different horizontal travel delays; deskew compensates before presenting a group of outputs together. The exact cycle schedule must be checked against RTL waveforms rather than inferred from this conceptual explanation.

## Theory: parallelism, data reuse and performance

### Spatial computing and dataflow

A serial dot-product engine reuses one arithmetic unit over time. A systolic architecture distributes that computation across processing elements: different stages work on different operands simultaneously while values move through regular neighbor-to-neighbor connections. The schedule matters as much as the arithmetic, because a correct product must meet the correct partial sum.

The main benefit is local reuse. A value forwarded between PEs can contribute to several operations without being fetched from external memory for each one. In **weight-stationary** operation, each PE retains a weight while activations and partial sums move; in **output-stationary** operation, a PE retains a partial output while operands arrive. These are data-placement choices, not different definitions of multiplication. [Kung, *Why Systolic Architectures?*](https://www.eecs.harvard.edu/~htk/publication/1982-kung-why-systolic-architecture.pdf)

### Latency is different from throughput

- **Latency:** elapsed cycles from an accepted input transaction to its corresponding valid result. Define whether weight loading and input capture are included.
- **Initiation interval:** cycles between accepting successive independent transactions.
- **Throughput:** useful completed results per unit time, measured over a stated interval.

Pipelining can overlap computations without making an individual result immediate. Filling the array, draining partial sums, loading weights and waiting for input data all affect end-to-end behavior. Consequently, a count of PEs is not a measured speedup. Published accelerator studies also distinguish arithmetic capacity from performance limited by operand movement and workload utilization. [Jouppi et al., *In-Datacenter Performance Analysis of a Tensor Processing Unit*](https://www.cs.cmu.edu/~18742/papers/Jouppi2017.pdf)

### Applying the distinction to this demonstration

The documented workload contains eight output positions and two active kernels. Each output is an eight-term dot product, so it contains **8 × 2 × 8 = 128 useful product contributions**. This is a workload count, not a cycle count. Zero-weight columns can execute arithmetic without adding useful work for these two kernels.

For an illustrative array with `P` active MAC units, clock frequency `f` and one MAC accepted per unit per cycle, the arithmetic ceiling is `P × f` MAC/s. Counting a multiplication and addition separately doubles the operation count; the convention must be stated. Actual useful throughput also depends on occupancy, stalls and transaction overhead. This repository does not yet provide the timing trace needed to calculate that rate for the implementation.

A meaningful future report should therefore pair a correct-output check with total transaction cycles, first-result latency, initiation interval and the number of useful outputs. This connects the mathematical work to observed RTL behavior without confusing theoretical capacity with measured performance.

## Module responsibilities

| Module | Responsibility | Evidence boundary |
|---|---|---|
| `Conv2Ch2x2_SystolicArray_Top` | Coordinates weight loading, computation and draining; connects the fixed demonstration inputs to the datapath | Top-level RTL inspected |
| `im2col_addr_gen` | Supplies the address sequence for sliding windows | Role documented; module body not retrieved |
| `S2P_Skew_OneShot` | Captures serial window elements and staggers their entry | Role documented; module body not retrieved |
| `Processing_Element` | Stores a weight, forwards data and combines a product with an incoming partial sum | RTL inspected |
| `Systolic_Array_8x8` | Connects PEs into the 8×8 mesh and instantiates output alignment | RTL inspected |
| `Deskew_8x8` | Aligns staggered column outputs | Instantiation inspected; module body not retrieved |

The table describes logical responsibilities, not a new claim that every block has passed verification. Sources: [project block descriptions](https://github.com/abhineeth007/systolic-array-8x8#architecture), [array wiring](https://github.com/abhineeth007/systolic-array-8x8/blob/main/Codes/Systolic_Array_8x8.v).

## MAC arithmetic and numeric limits

The PE's operation is **next partial sum = incoming partial sum + input × stored weight**. It does not simply accumulate into its own previous output: partial sums travel between PEs. For an illustrative input of −3, weight 2 and incoming partial sum 5, the next result is −1. [PE implementation](https://github.com/abhineeth007/systolic-array-8x8/blob/main/Codes/Processing_Element.v)

An 8-bit signed operand represents −128 through 127. A signed 16-bit result represents −32,768 through 32,767. Although one full 8×8-bit signed product fits in 16 bits, the sum of eight unrestricted products may not: eight instances of `(−128) × (−128)` sum to **131,072**. That value needs at least 19 signed bits.

Consequently, the default 16-bit sum width must not be presented as overflow-safe for all possible 8-bit data and weights. The inspected PE contains no explicit saturation or overflow flag. Extending this beyond the small built-in example should include deliberate accumulator-width selection and boundary-value tests. Merely changing a width parameter does not establish end-to-end correctness.

## Repository guide

- `Codes/`: six design modules, four testbenches, a Genus script, a mapped-netlist file, and constraint/power files.
- `Waveform Ouputs/`: images named for PE, im2col and top-level waveforms, alongside schematic and Genus area/power/timing images. The directory spelling is retained here to match the source.
- `Innovus_Timing_Reports/`: a report directory listed at the repository root; its contents were not accessible during this review.

Sources: [code inventory](https://github.com/abhineeth007/systolic-array-8x8/tree/main/Codes), [image inventory](https://github.com/abhineeth007/systolic-array-8x8/tree/main/Waveform%20Ouputs).

## Verification status and next steps

Testbench files are present for the processing element, address generator, serial/skew block, and integrated accelerator. Their contents were not retrievable during this review, so self-checking assertions and coverage have not been established. Image filenames are evidence of uploaded artifacts, not independently reproduced correctness results.

The existing README gives these expected output sequences for the built-in demonstration:

| Kernel | Expected values, in output-position order |
|---|---|
| 0 | −9, −11, −13, −15, −19, −21, −23, −25 |
| 1 | 2, 4, 6, 8, 12, 14, 16, 18 |

These are documented expectations, not newly observed simulator results. [Expected outputs](https://github.com/abhineeth007/systolic-array-8x8#expected-outputs)

To reproduce the RTL demonstration, compile the six design modules with `tb_conv2ch2x2_systolic_array.v`, selecting the testbench as the simulation top. Do not include the mapped `*_finalnet.v` in the behavioral RTL source list. Record the simulator/version, command, exit status, output checks and waveform before claiming a passing run.

### Suggested verification ladder — not yet executed

1. **PE arithmetic:** check positive, negative and zero operands; loading versus holding weights; reset; and values near numeric limits.
2. **Address sequence:** compare every emitted window address with an independently computed reference; verify window order and valid/done behavior.
3. **Capture and alignment:** use distinguishable lane values to check capture boundaries, launch timing, skew and deskew without relying on coincidentally equal samples.
4. **Integrated demo:** compare every valid output against a software dot-product model, check all eight output positions and both kernels, and verify zero-weight columns.
5. **Control robustness:** check reset during activity, repeated runs and start behavior. Define the supported interface contract before deciding how unexpected start pulses should behave.
6. **Regression evidence:** save a reproducible command, tool version, assertions/checker results and failing seeds where randomized tests are used.

This is a recommended validation plan, not a description of established testbench coverage. An output waveform that looks plausible is not equivalent to a self-checking regression.

## Synthesis flow and reproducibility gap

`run_genus.tcl` reads the six RTL files, elaborates the top, checks unresolved references, and runs generic synthesis, mapping and optimization. It requests timing, area and power reports plus a netlist and output constraints.

Before rerunning:

1. Configure the locally available standard-cell library; the script currently references an installation-specific library path and `slow.lib`.
2. Resolve the constraint-file mismatch: the script reads `systolic_constraints.sdc`, but that filename is absent from the inspected `Codes/` listing. A differently named `Conv2Ch2x2_SystolicArray_Top_block.sdc` is listed; do not assume it is an equivalent input constraint set.
3. Run from a directory where the script's relative RTL paths resolve.

No frequency, area, power, timing closure, FPGA deployment or tape-out result is asserted here. [Genus script](https://github.com/abhineeth007/systolic-array-8x8/blob/main/Codes/run_genus.tcl)

### How to interpret implementation reports

Synthesis translates RTL into technology-mapped cells; physical design subsequently places and routes those cells. A successful elaboration alone does not establish timing closure. Any published result should identify the exact revision, tool, library/corner, clock and I/O constraints, and whether the result is pre-layout or post-route.

For this design, constant tensor/weight values and zero-weight columns may allow synthesis to simplify logic. Therefore, 64 instantiated RTL PEs must not be equated with 64 surviving physical multipliers, nor should a fixed-demo area figure be treated as representative of a programmable accelerator. This is an optimization consideration, not a measured observation of the supplied netlist.

## Implemented scope versus future extensions

**Supported by inspected RTL:** hierarchical PE/array structure, stationary weights during computation, fixed demonstration lookup inputs, and connections for aligned outputs and validity.

**Present but not independently reproduced:** testbench files, synthesis script, netlist/constraint filenames, and uploaded waveform/report images.

**Possible future work, not implemented claims:**

- Add an external activation/weight interface and a clearly specified transaction protocol.
- Generalize dimensions, kernel count and padding with matching verification.
- Select overflow-safe sum widths or explicitly specify quantization/saturation behavior.
- Add automated self-checking simulation and regression automation.
- Produce reproducible implementation reports and compare resource/latency trade-offs under identical constraints.

The educational value lies in connecting convolution arithmetic to spatial dataflow and RTL control. Any résumé or portfolio performance claim should wait for reproducible evidence and a confirmed contribution statement.

## Provenance and attribution

Original public repository: [abhineeth007/systolic-array-8x8](https://github.com/abhineeth007/systolic-array-8x8).

No license file was visible in the inspected root listing. Preserve the source link and existing notices, and confirm contributor credit and permission before republishing code. This draft does not assign sole authorship or select a license.
