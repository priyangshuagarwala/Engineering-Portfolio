# NSFET: Ongoing TCAD Research

An ongoing nanosheet FET (NSFET) simulation project using technology computer-aided design (TCAD). This portfolio selection focuses on reading terminal electrical characteristics and presenting simulation outputs. The specific research question and device implementation remain confidential.

## Tools and workflow

- **Synopsys Sentaurus Workbench:** organizing the simulation workflow.
- **Sentaurus Device:** producing terminal current and small-signal electrical outputs.
- **Sentaurus Visual:** inspecting curves on linear and logarithmic scales and examining derivative characteristics.

The selected outputs cover transfer characteristics, output characteristics, current derivatives, and gate-capacitance sweeps. Together, they illustrate different ways to examine a simulated device: how current responds to gate bias, how it responds to drain bias, and how gate capacitance varies during a voltage sweep.

## Device theory behind the terminal curves

The following concepts are general MOS-device theory, not a disclosure of this project's device implementation or an extraction of new results.

### Gate control and current flow

The gate voltage changes the semiconductor's electrostatic potential and mobile charge. The drain-to-source bias drives carrier transport, producing drain current. This separates two useful questions: an **Iᴅ–Vɢ** sweep examines gate control at a fixed drain bias; an **Iᴅ–Vᴅ** sweep examines drain response at a fixed gate bias. Source and other terminal conditions must also be specified.

In an elementary long-channel MOS model, small drain bias gives a roughly resistive response; larger drain bias leads toward current saturation. Real devices need not follow the ideal square-law model, and a flatter output curve does not imply perfectly constant current. These concepts provide a starting point for interpretation, not a fitted model for the selected plots. [Stanford EE 116: MOS capacitors and MOSFETs](https://poplab.stanford.edu/files/ee116/L30to32_MOScapMOSFET.pdf)

### Local slopes and small-signal behavior

At a chosen DC operating point, transconductance and output conductance are **partial derivatives**:

- **gₘ = ∂Iᴅ/∂Vɢₛ**, holding Vᴅₛ and other independent biases fixed.
- **gᴅₛ = ∂Iᴅ/∂Vᴅₛ**, holding Vɢₛ and other independent biases fixed.

For sufficiently small voltage changes, with other terminals fixed, **ΔIᴅ ≈ gₘΔVɢₛ + gᴅₛΔVᴅₛ**. Both conductances have units of A/V, or siemens, when current is unnormalized. These are local sensitivities, not the DC ratios Iᴅ/Vɢₛ or Iᴅ/Vᴅₛ. A large gₘ means stronger gate control of incremental current at that operating point; finite gᴅₛ means the drain current still responds to drain voltage. Neither quantity alone establishes complete circuit performance.

A DC-curve slope and a finite-frequency AC response also require care: the AC response is evaluated around a DC bias and may be complex-valued. Record the operating point, perturbation frequency and definition before comparing them. [COMSOL: Small-Signal Analysis of a MOSFET](https://doc.comsol.com/6.3/doc/com.comsol.help.models.semicond.mosfet_small_signal/mosfet_small_signal.html)

### Capacitance and comparison conditions

Capacitance describes incremental charge response to voltage, rather than the current response described by gₘ. In the simple MOS-capacitor picture, changing gate bias changes charge distribution through accumulation, depletion and inversion. A transistor has multiple terminals, so its capacitance must be read with the terminal conditions and coefficient definition in mind. [Stanford EE 116](https://poplab.stanford.edu/files/ee116/L30to32_MOScapMOSFET.pdf)

For this portfolio, a sound comparison checklist is: identical DC bias conditions, temperature, AC frequency, current/capacitance normalization, sign convention and extraction method. Distinguish a terminal coefficient such as **c(g,g)** from an assumed isolated two-terminal capacitor. Keep numerical step size and differentiation settings with derivative results. Without those details, discuss qualitative trends rather than assigning a threshold, gain, switching speed or frequency-dispersion mechanism to these screenshots.

## How to read the graphs

These are original exported plots, not redrawn or enhanced results. Their axes are labeled only **X** and **Y**. The quantity descriptions below are interpretations based on supplied filenames, visible legends, and corresponding terminal-data fields. **The images do not print units or normalization, and neither is verified here.** Do not interpret plotted current as current per unit width or use these screenshots alone for quantitative benchmarking. The [graph guide](graph-guide.csv) records the interpretation basis and limitations for each image.

### Transfer characteristic: linear and logarithmic views

The transfer curves show drain total current against the gate-voltage sweep. The linear view emphasizes the larger-current portion of the sweep; the logarithmic-current view makes the smaller-current portion visible across several orders of magnitude. They are complementary presentations, not independent validation results.

![Transfer characteristic on a linear current scale](graphs/transfer-linear.png)

![Transfer characteristic on a logarithmic current scale](graphs/transfer-log.png)

### Output characteristic: linear and logarithmic views

The output curves show drain total current against the drain-voltage sweep. The linear plot shows a steep initial rise followed by a flatter region. The logarithmic view expands the small-current end of the characteristic. These plots are not a family of curves at multiple disclosed gate biases.

![Output characteristic on a linear current scale](graphs/output-linear.png)

![Output characteristic on a logarithmic current scale](graphs/output-log.png)

### Current-derivative characteristics

The supplied transconductance plot represents the derivative of drain current with respect to gate voltage, commonly written as **gₘ = dIᴅ/dVɢ**. Its visible curve rises, reaches a maximum, and then declines over the plotted sweep. The output-conductance plot represents the derivative with respect to drain voltage, **gᴅₛ = dIᴅ/dVᴅ**, and decreases across its sweep. Both legends mark the current expression with a prime. These derivative plots are useful for discussing local sensitivity, but numerical differentiation settings and uncertainty are not documented in this public selection.

![Transconductance characteristic](graphs/transconductance.png)

![Output-conductance characteristic](graphs/output-conductance.png)

### Gate-capacitance characteristics

The two capacitance plots show the **c(g,g)** terminal capacitance coefficient against a gate-voltage sweep. The source labels distinguish a low-frequency run marked **LF100** and a high-frequency run marked **1MHz**. Both visible curves increase more strongly toward the positive end of the sweep. They are shown separately; their apparent similarity is not a demonstrated frequency-independence result.

![Low-frequency gate-capacitance characteristic](graphs/capacitance-low-frequency.png)

![High-frequency gate-capacitance characteristic](graphs/capacitance-high-frequency.png)

## What this project demonstrates

- Organizing and interpreting several types of terminal-characteristic output.
- Using linear and logarithmic scales to inspect different current ranges.
- Connecting current slopes with transconductance and output conductance.
- Distinguishing current–voltage and small-signal capacitance analyses.
- Preparing a limited public presentation while protecting confidential design work.

## Status and limitations

Research is in progress. These are illustrative simulation outputs, not measured hardware results or a validated device model. This selection does not establish mesh convergence, calibration against measurements, reproducibility, extracted performance metrics, or superiority over another device. No quantitative threshold-voltage, subthreshold-swing, gain, or on/off-ratio claim is made.

## Public files and confidentiality

This folder contains this project explanation, a graph-guide CSV, and eight selected terminal-characteristic images. It is a presentation package, not a runnable simulation release.

The research topic, device dimensions, geometry, structure design, spatial field views, material comparisons, meshes, process/structure definitions, solver decks, raw simulation databases, logs, and original research archive are not included. No original simulation file was selected as a safe basic-file release; documentation and graph exports are the public subset.
