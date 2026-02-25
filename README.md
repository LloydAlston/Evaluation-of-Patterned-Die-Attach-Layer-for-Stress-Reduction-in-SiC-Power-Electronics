# Evaluation of Patterned Die-Attach Layer for Stress Reduction in SiC Power Electronics

> **CAx Techniques in Automotive Engineering** · Technische Hochschule Ingolstadt  
> [@aditrajv](https://github.com/aditrajv) · [@LloydAlston](https://github.com/LloydAlston) · International Automotive Engineering · July 2025
---

## Overview

This project investigates the **thermo-mechanical reliability** of Silicon Carbide (SiC) power electronic modules used in electric and hybrid vehicles. Using ANSYS Workbench, two packaging designs were evaluated under JEDEC-standard cyclic thermal loading to determine which substrate configuration minimizes stress in the die-attach (DA) layer — a critical failure-prone interface in high-performance power modules.

**Key Finding:** Introducing a strategic gap in the copper substrate **(Design B)** achieved a **~31% reduction in peak Von-Mises stress** compared to the standard design **(Design A)**.

---

## Table of Contents

- [Problem Statement](#problem-statement)
- [Designs Compared](#designs-compared)
- [Tools & Software](#tools--software)
- [Simulation Methodology](#simulation-methodology)
- [Key Results](#key-results)
- [Design of Experiments (DOE)](#design-of-experiments-doe)
- [Conclusions](#conclusions)
- [References](#references)

---

## Problem Statement

SiC-based power modules operate at high voltages, temperatures (up to 150°C), and switching frequencies. The layered structure of the module — SiC die, sintered silver die-attach, copper substrate, heat sink — contains materials with mismatched **Coefficients of Thermal Expansion (CTE)**. Under cyclic thermal loading, this mismatch generates interfacial stress that leads to:

- Crack initiation and propagation
- Delamination at bonding interfaces
- Long-term creep and fatigue failure

The DA layer (sintered silver) is the most vulnerable region and the primary focus of this study.

---

## Designs Compared

### Design A — Standard (No Gap)

| Layer | Part | Dimensions (mm) | Material |
|-------|------|-----------------|----------|
| 01 | Die | 5 × 1 × 0.1 | Silicon Carbide (SiC) |
| 02 | Die Attach Layer | 5 × 1 × 0.05 | Sintered Silver (Ag) |
| 03 | Copper Substrate | 20 × 20 × 1 | Copper (Cu) |
| 04 | Heat Sink | 30 × 30 × 3 | Copper hard, C10100 |

### Design B — With Substrate Gap

Identical to Design A, with the addition of a **copper substrate gap** (21 × 0.055 × 0.1 mm) positioned directly beneath the DA region to mechanically decouple the DA layer from the substrate.

---

## Tools & Software

| Tool | Purpose |
|------|---------|
| **ANSYS SpaceClaim** | CAD modeling (parametric geometry) |
| **ANSYS Workbench** | FEA — linear & nonlinear structural analysis |
| **ANSYS OptiSLang** | Design of Experiments (DOE) & sensitivity analysis |

---

## Simulation Methodology

### Material Properties

| Material | Young's Modulus (GPa) | Poisson Ratio | CTE (°C⁻¹) |
|----------|-----------------------|---------------|------------|
| SiC | 501 | 0.45 | 3.4 × 10⁻⁶ |
| Sintered Ag | 7 | 0.37 | 2.0 × 10⁻⁵ |
| Copper (Cu) | 110 | 0.35 | 1.7 × 10⁻⁵ |
| Copper C10100 hard | 126 | 0.345 | 1.67 × 10⁻⁵ |

### Thermal Cycling Profile (JEDEC Standard JESD22-A104F.01, Condition C)

```
+150°C ─────┐              ┌─────── +150°C
            │              │
            │  Ramp Down   │  Ramp Up
            │  (900s)      │  (900s)
            └──── -65°C ───┘
                  Dwell
                  (900s)
```

- **Temperature range:** −65°C to +150°C  
- **Stress-free reference:** 150°C (DA sintering temperature)  
- **Total cycles simulated:** 2 complete cycles (7200 seconds)

### Two-Phase Analysis

**Phase 1 — Linear Structural Analysis**  
Baseline stress distribution using elastic material behavior across all layers. Establishes comparative stress levels between Design A and Design B.

**Phase 2 — Nonlinear Analysis with Anand Viscoplastic Model**  
The sintered silver DA layer is modeled with the Anand creep model to capture:
- Time- and temperature-dependent creep deformation
- Plastic strain accumulation during repeated cycling
- Stress relaxation during high-temperature dwell phases

#### Anand Model Parameters for Sintered Ag

| Parameter | Symbol | Value | Unit |
|-----------|--------|-------|------|
| Pre-exponential factor | A | 9.81 | s⁻¹ |
| Activation energy / Gas constant | A/R | 5706 | K |
| Multiplier of stress | ξ | 11 | — |
| Strain rate sensitivity | m | 0.66 | — |
| Deformation resistance saturation | s | 67.3 | MPa |
| Deformation resistance exponent | n | 0.00326 | — |
| Hardening/softening constant | h₀ | 1.58 × 10⁴ | MPa |
| Strain rate sensitivity of hardening | a | 1 | — |
| Initial deformation resistance | s₀ | 2.77 | MPa |

### Mesh Strategy

| Component | Mesh Type | Rationale |
|-----------|-----------|-----------|
| DA Layer | Hex Dominant | Handles complex patterned geometry; resolves stress gradients |
| SiC Die & Heat Sink | Multizone Hexahedral | Superior accuracy for regular geometry |
| Copper Substrate | Tetrahedral | Handles geometric complexity from gap feature |

- **Global mesh size:** 500 µm (convergence confirmed)  
- **DA layer local refinement:** 25 µm (below minimum feature size of 50 µm)

---

## Key Results

### Linear Simulation

| Design | Max Von-Mises Stress (MPa) | Peak Time |
|--------|---------------------------|-----------|
| Design A (No Gap) | **456.51** | 5400 s |
| Design B (With Gap) | **313.20** | 5400 s |
| **Reduction** | **~31%** | — |

### Nonlinear Simulation (Anand Model)

| Metric | Design A | Design B | Reduction |
|--------|----------|----------|-----------|
| Max Von-Mises Stress | 71.3 MPa | 57.4 MPa | **~20%** |
| Equivalent Plastic Strain | 0.1113 µm/µm | 0.0699 µm/µm | **~38%** |

> The nonlinear simulation shows lower absolute stress values than the linear case — this is expected. Elastic-only models overestimate stress because they cannot account for plastic yielding and stress relaxation in the sintered silver DA layer.

---

## Design of Experiments (DOE)

An optimization study was conducted in **ANSYS OptiSLang** (250 iterations) to find the optimal combination of:
- **DA Thickness** (range: 0.045 – 0.065 mm)
- **Substrate Gap Width** (range: 0.45 – 0.65 mm)

### Sensitivity Analysis Results

| Input Parameter | Contribution to Max Stress | Contribution to Avg Stress |
|-----------------|---------------------------|---------------------------|
| Gap Width | **63.1%** | ~0% |
| DA Thickness | **13.8%** | **97.8%** |

**Optimal design zone:** DA ≈ 0.046 mm, Gap ≈ 0.58 mm

The **substrate gap width** is the dominant lever for reducing peak stress, while **DA thickness** primarily controls average stress distribution uniformity.

---

## Conclusions

1. **Design B outperforms Design A** across both linear and nonlinear simulations — the substrate gap acts as a mechanical decoupling feature that redistributes and relieves thermally induced stress.

2. **The Anand viscoplastic model** provides a significantly more realistic prediction of long-term reliability than purely elastic models, capturing creep accumulation and stress relaxation in sintered silver.

3. **DOE-driven optimization** identified gap width as the most influential design parameter, contributing over 60% of peak stress variation — providing clear engineering guidance for future packaging design.

4. **Future work** could incorporate experimental validation, more thermal cycles, aging effects, vibration loading, and exploration of advanced die-attach interface materials.

---

## References

1. Ye, G., Fan, X. and Zhang, G. (2022). Practical aspects of thermomechanical modeling in electronics packaging: A case study with a SiC power package. *Microelectronics Reliability*, 132, pp.114514. https://doi.org/10.1016/j.microrel.2022.114514

2. JEDEC Solid State Technology Association (2023). *JESD22-A104F.01: Temperature Cycling*. https://www.jedec.org/standards-documents/docs/jesd-22-a104e

3. Gharaibeh, M.A. and Wilde, J. (2023). Applying Anand versus Garofalo creep constitutive models for simulating sintered silver die attachments in power electronics. *The Journal of Strain Analysis for Engineering Design*, 59(1), pp.31–43. https://doi.org/10.1177/03093247231190449

---

