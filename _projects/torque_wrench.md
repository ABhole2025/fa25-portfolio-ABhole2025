---
layout: project
title: "Torque Wrench Design Project – MAE 3270 (Fall 2025)"
description: "Design and analysis of an instrumented torque wrench for MAE 3270: Mechanics of Materials"
technologies: ["ANSYS", "Autodesk Fusion 360", "MATLAB"]
image: /assets/Screenshot 2025-12-06 at 18.38.17.png
---

For my class **MAE 3270: Mechanics of Materials**, I completed a semester-long design project to develop a **non-ratcheting, 3/8-inch drive instrumented torque wrench** rated for **600 in-lbf**.  
The objective of the project was to integrate solid mechanics theory, material selection, strain-based sensing, and finite element analysis into a single functional mechanical design.

The scope of the project included:
- Material selection among steel, aluminum, and titanium alloys  
- Analytical stress, fatigue, and fracture mechanics evaluation  
- CAD modeling performed in **Autodesk Fusion 360**  
- Finite element analysis performed in **ANSYS**

---

## Project Requirements

The minimum project requirements were:

- Minimum electrical output of **1.0 mV/V** at the rated torque of **600 in-lbf**
- Static safety factor **X₀ ≥ 4** (yield or brittle failure)
- Fracture safety factor **Xᴷ ≥ 2** assuming an initial surface crack of depth **0.04 in (1 mm)**
- Fatigue safety factor **Xˢ ≥ 1.5**
- Use of a **steel, aluminum, or titanium alloy**

---

## Concept and Overall Design

The torque wrench was designed as a straight-handle, non-ratcheting beam subjected primarily to bending under applied torque.  
This configuration produces a predictable, nearly linear strain field suitable for strain-gauge-based torque measurement while maintaining geometric simplicity and structural robustness.

A rectangular cross-section was selected to balance:
- Bending stiffness  
- Strain sensitivity  
- Available surface area for strain gauge installation  
---

## CAD Model and Dimensions

The torque wrench was modeled in **Fusion 360** with all key dimensions defined parametrically. The CAD model explicitly shows:
- Overall handle length  
- Cross-sectional dimensions (*height h* and *width b*)  
- Dedicated strain gauge mounting surface  

These dimensions were used directly in both the analytical calculations and the finite element model.

<img src="{{ '/assets/CAD.png' | relative_url }}"
     alt="Fully dimensioned CAD model"
     class="project-image">

<img src="{{ '/assets/CAD dimension.png' | relative_url }}"
     alt="Gauge mounting surface detail"
     class="project-image">

---

## Material Selection

The selected material for the final design was **AISI A2 Tool Steel**, chosen for its high stiffness, excellent fatigue resistance, and ability to meet fracture safety requirements using a compact cross-section.

Mechanical properties used in analysis:
- Young’s modulus: **E = 30.3 Msi**  
- Ultimate strength: **σᵤ = 261 ksi**  
- Fatigue strength: **σ_f = 88.2 ksi**  
- Fracture toughness: **Kᴵᶜ = 17.6 ksi√in**

This material provided sufficient strain output for instrumentation while maintaining large safety margins against static, fatigue, and crack-growth failures.

---

## Loads and Boundary Conditions

The applied loading corresponds to a torque of **600 in-lbf**, modeled in the FEM as an equivalent force applied at the end of the handle.  
The square drive end of the wrench was fully constrained in translation and rotation.

This loading configuration produces bending about a single neutral axis, consistent with the assumptions used in the analytical beam-bending calculations.

<img src="{{ '/assets/images/radio-machine.jpg' | relative_url }}"
     alt="Loads and boundary conditions diagram"
     class="project-image">

---

## Finite Element Analysis – Strain Results

Finite element analysis was used to extract **normal strain aligned with the strain gauge grid direction** on the gauge mounting surface.

The strain field in the gauge region was found to be sufficiently uniform, validating the use of bonded strain gauges and the assumption of average strain measurement over the gauge area.

<img src="{{ '/assets/images/radio-machine.jpg' | relative_url }}"
     alt="Normal strain contour aligned with gauge direction"
     class="project-image">

---

## Finite Element Analysis – Stress Results

Maximum principal stress contours were examined to identify the peak stresses in the wrench under rated loading and to confirm compliance with design limits.

<img src="{{ '/assets/images/radio-machine.jpg' | relative_url }}"
     alt="Maximum principal stress contour plot"
     class="project-image">

---

## Strain Gauge Selection and Layout

Linear foil strain gauges with a carrier size of **7.6 mm × 5.8 mm** were selected.  
Four identical gauges were installed on a single face of the wrench to form a **full Wheatstone bridge** configuration.

The **0.700-inch-wide gauge mounting face** provides sufficient space to bond all four gauges with appropriate spacing, satisfying physical installation and manufacturability requirements.

<img src="{{ '/assets/images/radio-machine.jpg' | relative_url }}"
     alt="Strain gauge layout on wrench face"
     class="project-image">

---

## Torque Wrench Sensitivity (From FEM Strain Results)

Strain values extracted directly from the FEM results at the gauge locations were used to compute the electrical sensitivity of the torque wrench.

The full-bridge output was calculated using:

\[
V_{\text{out}} = GF \cdot \varepsilon \cdot 10^3 \quad \text{(mV/V)}
\]

Where:
- Gauge factor **GF = 2**
- ε is the FEM-predicted strain at the gauge location

Using FEM results:
- Strain at gauge location: **___ με**  
- Full-bridge output sensitivity: **___ mV/V**

This output exceeds the minimum required sensitivity of **1.0 mV/V**.

---

## Results Summary (FEM Values)

Key results obtained from finite element analysis:

- Maximum normal stress: **___ ksi**  
- Load-point deflection: **___ in**  
- Strain at gauge location: **___ με**  
- Full-bridge output sensitivity: **___ mV/V**

All project performance requirements were satisfied.

---

## Conclusions and Reflections

This project demonstrated the integration of mechanics of materials theory with finite element modeling and strain-based instrumentation.  
Strong agreement between analytical predictions and FEM results increased confidence in the final design.

Through this process, I gained experience balancing stiffness, strain sensitivity, and structural safety while translating theoretical requirements into a physically realizable mechanical instrument. Future work could include experimental calibration, electronics integration, and optimization of gauge placement for noise reduction.
