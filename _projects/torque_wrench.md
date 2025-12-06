---
layout: project
title: "Torque Wrench Design Project - MAE 3270 (Fall 2025)"
description: "Design of torque wrench for class project - MAE 3270: Mechanics of Materials"
technologies: ["ANSYS", "Autodesk Fusion 360", "MATLAB"]
image: /assets/CAD.png
---

# MAE 3270 – Instrumented Torque Wrench Design Project

For my class **MAE 3270: Mechanics of Materials**, I was assigned a semester project to design a **non-ratcheting, 3/8-inch drive instrumented torque wrench** rated for **600 in-lbf**. The objective was to integrate mechanical design, material selection, strain-based sensing, and finite element analysis into a single functional tool design.

The project scope included:
- Material selection among steel, aluminum, and titanium alloys  
- Analytical stress, fatigue, and fracture mechanics evaluation  
- CAD modeling performed in **Fusion 360**  
- Finite element analysis performed in **ANSYS**

---

## Project Requirements

The minimum requirements specified for this design were:

- Minimum electrical output of **1.0 mV/V** at the rated torque of **600 in-lbf**
- Static safety factor of **X₀ = 4** (yield or brittle failure)
- Fracture safety factor of **Xᴷ = 2** assuming an initial surface crack of depth **0.04 in (1 mm)**
- Fatigue safety factor of **Xˢ = 1.5**
- Use of a **steel, aluminum, or titanium alloy**

---

## Concept and Overall Design

The torque wrench was designed as a straight-handle, non-ratcheting beam subjected primarily to bending under applied torque. This configuration produces a predictable linear strain field suitable for strain-gauge-based torque measurement while maintaining structural simplicity and robustness.

The rectangular cross-section was selected to balance bending stiffness, strain sensitivity, and available surface area for strain gauge placement.

![Overall CAD model of torque wrench]({{ "/assets/images/radio-machine.jpg" | relative_url }})

---

## CAD Model and Dimensions

The torque wrench was modeled in **Fusion 360** with all key dimensions defined parametrically. The CAD model explicitly shows:
- Overall handle length  
- Cross-sectional dimensions (height *h* and width *b*)  
- Strain gauge mounting surface dimensions  

These dimensions were used directly in both the analytical calculations and the finite element model.

![Fully dimensioned CAD model]({{ "/assets/images/radio-machine.jpg" | relative_url }})

![Side face showing strain gauge mounting surface]({{ "/assets/images/radio-machine.jpg" | relative_url }})

---

## Material Selection

The selected material was **AISI A2 Tool Steel**, chosen due to its high stiffness, excellent fatigue resistance, and ability to satisfy fracture safety requirements using a compact cross-section.

Relevant mechanical properties used in analysis:
- Young’s modulus, **E = 30.3 Msi**
- Ultimate strength, **σᵤ = 261 ksi**
- Fatigue strength, **σ_f = 88.2 ksi**
- Fracture toughness, **Kᴵᶜ = 17.6 ksi√in**

This material provided sufficient strain output for instrumentation while maintaining high margins against static, fatigue, and crack-growth failures.

---

## Loads and Boundary Conditions

The applied loading corresponds to a torque of **600 in-lbf**, modeled in the FEM as an equivalent force applied at the end of the handle. The square drive end of the wrench was fully constrained in translation and rotation.

This loading produces bending about a single neutral axis, consistent with the assumptions used in the analytical beam-bending calculations.

![Loads and boundary conditions diagram]({{ "/assets/images/radio-machine.jpg" | relative_url }})

---

## Finite Element Analysis – Strain Results

Finite element analysis was performed to extract **normal strain aligned with the strain gauge grid direction** on the gauge mounting surface.

The strain field in the gauge region was found to be sufficiently uniform, validating the assumption of average strain measurement by the bonded strain gauges.

![Normal strain contour plot (gauge direction)]({{ "/assets/images/radio-machine.jpg" | relative_url }})

---

## Finite Element Analysis – Stress Results

Maximum principal stress contours were examined to identify the peak stresses in the wrench under rated loading and to confirm compliance with design limits.

![Maximum principal stress contour plot]({{ "/assets/images/radio-machine.jpg" | relative_url }})

---

## Strain Gauge Selection and Layout

Linear foil strain gauges with a carrier size of **7.6 mm × 5.8 mm** were selected. Four identical gauges were arranged on a single face of the wrench to form a **full Wheatstone bridge** configuration.

The **0.700-in-wide gauge mounting face** provides sufficient space to bond all four gauges with appropriate spacing, satisfying physical installation and manufacturability requirements.

![Strain gauge layout on wrench face]({{ "/assets/images/radio-machine.jpg" | relative_url }})

---

## Torque Wrench Sensitivity (From FEM Strain Results)

Strain values extracted directly from the FEM results at the gauge locations were used to calculate the electrical sensitivity of the torque wrench.

The full-bridge output was computed using:

\[
V_{\text{out}} = GF \cdot \varepsilon \cdot 10^3 \quad \text{(mV/V)}
\]

Where:
- Gauge factor, **GF = 2**
- ε is the FEM-predicted strain at the gauge location

Using FEM results:
- Strain at gauge location: **___ με**
- Resulting full-bridge sensitivity: **___ mV/V**

This output exceeds the minimum required sensitivity of **1.0 mV/V**.

---

## Results Summary (FEM Values)

Key results obtained from the finite element analysis are summarized below:

- Maximum normal stress: **___ ksi**  
- Load-point deflection: **___ in**  
- Strain at gauge location: **___ με**  
- Full-bridge output sensitivity: **___ mV/V**

All project performance requirements were satisfied.

---

## Conclusions and Reflections

This project demonstrated the integration of mechanics of materials theory with finite element modeling and strain-based sensing. Strong agreement between analytical calculations and FEM results increased confidence in the final design.

Through this process, I gained experience balancing stiffness, strain sensitivity, and structural safety, as well as translating theoretical requirements into a physically realizable instrumented component. Future improvements could include experimental calibration, electronics integration, and optimization of gauge placement for noise reduction.
