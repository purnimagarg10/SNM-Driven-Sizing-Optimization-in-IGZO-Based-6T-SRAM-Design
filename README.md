# SNM-Driven Sizing Optimization in IGZO-Based 6T SRAM Design

[![Institute](https://img.shields.io/badge/IIT-Ropar-blue)](https://www.iitrpr.ac.in/)
[![Tool](https://img.shields.io/badge/Tool-Cadence%20Virtuoso-orange)]()
[![Domain](https://img.shields.io/badge/Domain-VLSI%20%7C%20Memory%20Design-brightgreen)]()
[![Status](https://img.shields.io/badge/Status-Completed-success)]()

> Undergraduate research project at **IIT Ropar** analyzing how transistor sizing affects Static Noise Margin (SNM) in 6T SRAM cells, and comparing conventional bulk-Si transistors against **IGZO (Indium-Gallium-Zinc-Oxide) thin-film transistors (TFTs)**, with theoretical predictions validated against simulation results.

---

## Table of Contents
- [Overview](#overview)
- [Motivation](#motivation)
- [Background](#background)
- [Methodology](#methodology)
- [Case Studies](#case-studies)
- [Results](#results)
- [Key Findings](#key-findings)
- [Repository Structure](#repository-structure)
- [Tools & Technologies](#tools--technologies)
- [Future Work](#future-work)
- [References](#references)
- [Author](#author)

---

## Overview

This project investigates the impact of **transistor sizing** on the three critical stability metrics of a 6T SRAM cell:

- **HSNM** — Hold Static Noise Margin
- **RSNM** — Read Static Noise Margin
- **WSNM** — Write Static Noise Margin (write margin)

The study is carried out in two stages:
1. **Bulk-Si sizing sweep** — six different sizing combinations of the pull-up, access, and pull-down transistor widths were designed and simulated on a **conventional bulk-Si 6T SRAM cell**, to observe how each device's relative strength shapes the butterfly curve and the three SNMs.
2. **IGZO optimized design** — rather than re-running all six combinations on IGZO TFTs, the sizing insight from the best-performing bulk-Si case was carried over to design a single **optimized IGZO thin-film-transistor 6T SRAM cell**, which was then evaluated for the same three margins. IGZO TFTs are of interest here for their potential in low-leakage, back-end-of-line (BEOL)-compatible, and monolithic 3D memory applications.

Theoretical predictions from first-principles inverter/VTC analysis were used throughout to explain *why* each sizing choice shifted the margins the way it did, and were checked against the simulated SNM values.

## Motivation

As technology scales down, **leakage power and process variation** increasingly threaten SRAM stability — historically the most area- and yield-sensitive block in any SoC. IGZO TFTs have emerged as a promising candidate for **BEOL-integrated memory** (stacked above logic in the back-end), offering:

- Ultra-low off-state leakage (sub-fA/µm), attractive for retention-critical applications
- Compatibility with 3D monolithic integration (fabricated at low temperature, above the logic layer)
- A fundamentally different device physics regime than bulk-Si, which changes how sizing rules affect stability

This project asks a practical design question: **which transistor (pull-up, access, or pull-down) should be strengthened, and by how much, to get the best balance of HSNM/RSNM/WSNM** — and once that sizing heuristic is learned from a conventional bulk-Si cell, **does it carry over cleanly to an IGZO TFT cell**, or does the different device physics (subthreshold slope, mobility, threshold voltage) shift the optimum?

## Background

**6T SRAM Cell:** Two cross-coupled CMOS inverters (PU1–PD1, PU2–PD2) form the storage bit, and two access transistors (PG1, PG2) connect the storage nodes to the bitlines during read/write.

**Butterfly Curve & SNM:** Overlaying the voltage transfer characteristics (VTC) of the two cross-coupled inverters produces a "butterfly" shape. The side length of the largest square that fits inside each lobe of this curve is the Static Noise Margin — a direct measure of how much DC noise the cell can tolerate before flipping state.

**Why sizing matters:** Strengthening the pull-down transistor relative to the access transistor makes the storage node harder to disturb during a read, widening RSNM. Weakening the pull-up relative to the access transistor makes it easier to flip the node during a write, widening WSNM. These two goals pull in opposite directions — which is exactly what the six sizing cases in this project are designed to expose.

## Methodology

1. **Schematic design** of the 6T SRAM cell in Cadence Virtuoso, starting with bulk-Si device models.
2. **DC sweep / butterfly curve extraction** for hold and read conditions; **transient analysis** for write operation.
3. **Six bulk-Si sizing cases**, each varying the width of one or more of the pull-up, access, and pull-down transistors (see [Case Studies](#case-studies) table below).
4. **SNM extraction** using the maximum-square method on each butterfly curve lobe, for all six cases — computed in MATLAB from the extracted node-voltage data.
5. **Best-case identification**: the sizing combination giving the best overall HSNM/RSNM/WSNM balance is identified.
6. **IGZO redesign**: the same sizing ratio is applied to an IGZO TFT version of the 6T cell, which is simulated once and compared against the bulk-Si best case.
7. **Cross-validation**: theoretical/expected trends (from VTC slope and inverter gain arguments) compared against the extracted simulation values to confirm/explain any deviation.

## Case Studies

### Stage 1 — Bulk-Si sizing sweep (6 cases)

All widths in µm; minimum-length devices used throughout so width alone sets the relative drive strength.

| Case | Pull-Up | Access | Pull-Down | Design Intent |
|------|---------|--------|-----------|---------------|
| 1 | 1u | 1u | 1u | Baseline — all transistors equal width |
| 2 | 4u | 1u | 1u | Strong pull-up only |
| 3 | 1u | 4u | 1u | Strong access transistor only |
| 4 | 1u | 1u | 4u | Strong pull-down only |
| 5 | 1u | 4u | 4u | Strong access + strong pull-down |
| 6 | 1u | 2u | 4u | Moderate access + strong pull-down |

### Stage 2 — IGZO optimized design (1 case)

Rather than repeating all six sweeps, the best-performing sizing ratio identified above was carried over to build a single optimized IGZO TFT 6T SRAM cell.

## Results

A small, curated set of images is used here rather than one screenshot per case — the comparison chart tells the sizing story more clearly than six near-identical butterfly curves would.

### Best-Case vs Worst-Case RSNM (Bulk-Si)

| Best Case (Case 4) | Worst Case (Case 3) |
|---|---|
| ![Best case RSNM butterfly curve](results/best_case_highlights/best_rsnm_butterfly.png) | ![Worst case RSNM butterfly curve](results/best_case_highlights/worst_rsnm_butterfly.png) |

### SNM Trend Across All Six Bulk-Si Cases

![SNM comparison across six cases](results/comparison_plots/snm_vs_case.png)


## Key Findings

- Optimized sizing (vs. equal/unoptimized) improved RSNM by **88%** in the bulk-Si sweep.
- IGZO TFT cell showed [wider / narrower] noise margins than the best bulk-Si case at the same sizing ratio, attributable to [subthreshold slope / mobility / Vt differences].
- The sizing heuristic learned from bulk-Si [carried over well / needed adjustment] when applied to IGZO devices.
- Theoretical VTC-slope-based reasoning correctly predicted the *direction* of SNM change for each sizing case (e.g. strengthening the pull-down transistor should widen RSNM), and this was consistently confirmed by the simulated butterfly curves — validating the first-principles model qualitatively.

## Repository Structure

```
SNM-Driven-Sizing-Optimization-IGZO-6T-SRAM/
├── README.md
├── LICENSE
├── schematics/
│   ├── bulk-Si/                 # Bulk-Si 6T SRAM schematic screenshots
│   └── igzo/                    # IGZO TFT 6T SRAM schematic screenshots
├── simulations/
│   ├── bulk-si/
│   │   └── Case1/ ... Case6/    # Per-case: sizing, extracted data, mini-README
│   └── igzo-optimized/          # Single optimized IGZO case: schematic,
│                                  butterfly curves, transient plot, data
└── results/
    ├── comparison_plots/        # SNM-vs-case trend chart
    └── best_case_highlights/    # Best-case and worst-case RSNM butterfly curves
 
```

## Tools & Technologies

- **Cadence Virtuoso** — schematic capture, DC/transient simulation
- **MATLAB** — post-processing of extracted node voltages and computing SNM values from butterfly curves



## Future Work

- Extend sizing sweep to a finer CR/PR grid for a full design-space (Pareto) exploration.
- Add Monte Carlo / process-variation analysis on top of the six nominal cases.
- Explore hybrid cells (bulk-Si access transistors + IGZO storage inverters) as a middle ground.
- FPGA/array-level power and retention-time estimation using the extracted device parameters.

## References

1. E. Seevinck, F. J. List and J. Lohstroh, "Static-noise margin analysis of MOS SRAM cells," *IEEE JSSC*, 1987.
2. Florian De Roose, Kris Myny, Marc Ameys, Jan-Laurens P. J. van der Steen,
   Joris Maas, Joris de Riet, Jan Genoe, and Wim Dehaene,
   **"A Thin-Film, a-IGZO, 128b SRAM and LPROM Matrix With Integrated Periphery on Flexible Foil,"**
   *IEEE Journal of Solid-State Circuits*, vol. 52, no. 11, pp. 3095–3103,
   Nov. 2017.
   DOI: https://doi.org/10.1109/JSSC.2017.2731808

## Author

**Purnima Garg** — B.Tech, Electronics and Communication Engineering (VLSI), IIT Ropar
Project supervised by [Dr. Pardeep Duhan] — [Electrical Dept.]

*Feel free to connect on [LinkedIn](www.linkedin.com/in/purnima-garg-eng25) or reach out for questions about the methodology or design files.*

---
*This repository documents an academic research project. Device models and simulation setups reflect an undergraduate research context and are intended for educational/portfolio purposes.*
