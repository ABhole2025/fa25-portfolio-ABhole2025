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

## <span style="color:#A7BADB;">Project Requirements</span>

---

The minimum project requirements were:

- Minimum electrical output of **1.0 mV/V** from strain gauge at the rated torque of **600 in-lbf**
- Safety factor **X₀ ≥ 4** for failiure due to stress (yielding or brittle, depending on material)
- Fracture safety factor **Xᴷ ≥ 2** assuming an initial surface crack of depth **0.04 in (1 mm)**
- Fatigue safety factor **Xˢ ≥ 1.5**
- Use of a **steel, aluminum, or titanium alloy**

---

## <span style="color:#A7BADB;">Design process</span>

---

#### Hand Calculations Using MATLAB

The torque wrench was designed as a straight-handle, non-ratcheting beam subjected primarily to bending under applied torque, with a 3/8 inch drive. 

First, hand calculations were done to create a design that met the requiremets. Using material data from Granta, a handful of materials were selected from the steel, aluminum, and titanium alloys. The preliminary selection was doen by looking at the ration of their fatugue stress to their yield strength and ultimate strength. The, taking the relevant material properties, they were run through the following MATLAB script:

```matlab
%% WRENCH DESIGN ITERATOR
% ---------------------------------------------------------------
%
%   - Static safety factor X0 >= 4
%   - Fatigue safety factor XS >= 1.5
%   - Crack growth safety factor XK >= 2
%   - Strain-gauge sensitivity Vout >= 1.0 mV/V (half-bridge)
% ---------------------------------------------------------------

clear; clc;

%% ---------------------- FIXED INPUTS --------------------------
M = 600;          % torque (in-lbf)
L = 16;           % wrench length (in)
c = 1.0;          % gauge location from drive (in)
a = 0.04;         % crack depth (in)
GF = 2.0;         % gauge factor
V_required = 1.0; % minimum mV/V (half-bridge)

% min safety factors
X0_req = 4;
XS_req = 1.5;
XK_req = 2;

%% ------------------ GEOMETRY VALUES TO TRY --------------------
h_values = 0.5 : 0.01 : 1.0;   % thickness (in)
b_values = 0.5 : 0.01 : 1.0;   % width (in)

%% --------------------- MATERIAL PROPERTIES ---------------------
% Columns: [E (psi), su (psi), sfatigue (psi), KIC (psi·sqrt(in))]
% All values use the lowest of each provided range in Granta.

material_names = { ...
    'Aluminum 6061-T6', ...
    'Aluminum 7075-T6', ...
    'Ti-6Al-4V (aged)', ...
    'AISI A2 Tool Steel', ...
    'AISI S7 Tool Steel', ...
    'Aluminum 7020-T6', ...
    'AISI O6 Tool Steel',...
    'Ti-5Al-2.5Sn-0.5Fe, annealed'};

materials = [ ...
    9.66e6     34.8e3    17.4e3    27.3e3;   % Al 6061-T6
    10e6       66.7e3    24.9e3      24.2e3;   % Al 7075-T6
    16.1e6     148e3     87.4e3    74.6e3;   % Ti-6Al-4V aged
    30.3e6     261e3     88.2e3    17.6e3;   % A2 Tool Steel
    29.9e6     53.8e3    20.4e3    50.3e3;   % S7 Tool Steel
    10.2e6     46.1e3    18.4e3    30e3;     % Al 7020-T6
    29.6e6     270e3     91.2e3    17.1e3;  % O6 Tool Steel
    15.5e6     110e3     58.6e3    84.6e3]; %Ti-5Al-2.5Sn-0.5Fe, annealed

%% ---------------- STORAGE FOR VALID DESIGNS -------------------
valid = [];  
row = 0;

%% ------------------ 
for m = 1:size(materials,1)

    E = materials(m,1);
    su = materials(m,2);
    sfatigue = materials(m,3);
    KIC = materials(m,4);

    for h = h_values
        for b = b_values

            % Moment of inertia
            I = b * h^3 / 12;

            % Distance from neutral axis
            y = h/2;

            % bending stress
            sigma = M * y / I;   % psi
            X0 = su / sigma;

            % strain where strain gauge is placed
            F = M / L;                % force (lb)
            M_g = F * (L - c);        % local moment (in-lb)
            sigma_g = M_g * y / I;    % psi
            epsilon_g = sigma_g / E;  % strain
            epsilon_micro = epsilon_g * 1e6;

            % Fullbridge output
            Vout = (GF * epsilon_g) * 1e3;   % mV/V

            % Fatigue safety factor
            XS = sfatigue / sigma;

            % Crack growth safety factor
            F_K = 1.12;
            K_I = F_K * sigma * sqrt(pi * a);
            XK = KIC / K_I;

            %  Deflection
            defl = (F * L^3) / (3 * E * I);

            %---------------- CHECK REQUIREMENTS ----------------
            if X0 >= X0_req && XS >= XS_req && XK >= XK_req && Vout >= V_required && defl <= L/50 && XS >= 2 && XK <= 5 

                row = row + 1;
                valid(row).material = material_names{m};
                valid(row).h = h;
                valid(row).b = b;
                valid(row).Vout = Vout;
                valid(row).epsilon_micro = epsilon_micro;
                valid(row).X0 = X0;
                valid(row).XS = XS;
                valid(row).XK = XK;
                valid(row).deflection = defl;
            end
        end
    end
end

%% ----------------------- OUTPUT -------------------------------
if isempty(valid)
    fprintf('\nNo designs satisfy all requirements.\n');
else
    fprintf('\n%d valid designs found.\n', length(valid));

    % Convert to table for nicer viewing
    T = struct2table(valid);
    % Sort by highest sensitivity (Vout)
    T = sortrows(T, 'epsilon_micro', 'descend');

    fprintf('\nAll valid designs:\n');
disp(T(:, {'material','h','b','Vout','epsilon_micro','X0','XS','XK','deflection'}));

locked = struct( ...
    'material','AISI A2 Tool Steel', ...
    'h',0.63, ...
    'b',0.56, ...
    'Vout',1.0023, ...
    'epsilon_micro',501.14, ...
    'X0',16.114, ...
    'XS',5.4455, ...
    'XK',2.7369, ...
    'deflection',0.14481);

disp(locked);
   
end
```

This code displayed a large list of potential designs, giving the safety factors, strain gauge output, and the dimensions for h and b. there were a huge number of potential designs.

After picking one design, I ran it through this 'single design tester'

```matlab
%% ===================== MATERIAL PROPERTIES ==========================
% Columns: [E (psi), su (psi), sfatigue (psi), KIC (psi·sqrt(in))]
material_names = { ...
    'Aluminum 6061-T6', ...
    'Aluminum 7075-T6', ...
    'Ti-6Al-4V (aged)', ...
    'AISI A2 Tool Steel', ...
    'AISI S7 Tool Steel', ...
    'Aluminum 7020-T6', ...
    'AISI O6 Tool Steel', ...
    'Ti-5Al-2.5Sn-0.5Fe, annealed'};

materials = [ ...
    9.66e6     34.8e3    17.4e3    27.3e3;    % 6061-T6
    10e6       66.7e3    24.9e3    24.2e3;    % 7075-T6
    16.1e6     148e3     87.4e3    74.6e3;    % Ti-6Al-4V aged
    30.3e6     296e3     88.2e3    17.6e3;    % AISI A2 Tool Steel
    29.9e6     53.8e3    20.4e3    50.3e3;    % AISI S7
    10.2e6     46.1e3    18.4e3    30e3;      % 7020-T6
    29.6e6     270e3     91.2e3    17.1e3;    % AISI O6
    15.5e6     110e3     58.6e3    84.6e3];   % Ti-5Al-2.5Sn

%% ===================== SELECT MATERIAL =============================
material_name = 'AISI A2 Tool Steel';     % <<< YOUR DESIGN MATERIAL
idx = find(strcmp(material_names, material_name));

E        = materials(idx,1);
su       = materials(idx,2);
sfatigue = materials(idx,3);
KIC      = materials(idx,4);

%% ===================== GEOMETRY (YOUR VALUES) ======================
M = 600;           % torque (in-lbf)
L = 16;            % wrench length (in)
b = 0.7;          % <<< YOUR thickness (in)
h = 0.5;          % <<< YOUR width (in)
a = 0.04;          % crack depth (in)
GF = 2.0;          % gauge factor
V_required = 1.0;  % mV/V
c = 1.0;           % gauge location (in)

%% Safety factor requirements
X0_req = 4;
XS_req = 1.5;
XK_req = 2;

%% ===================== SECTION PROPERTIES ==========================
I = b * h^3 / 12;
y = h / 2;

%% ===================== STRESS & STATIC SF ==========================
sigma = M * y / I;
X0 = su / sigma;

%% ===================== STRAIN & OUTPUT =============================
F_handle = M / L;
M_g = F_handle * (L - c);
sigma_g = M_g * y / I;

epsilon_g = sigma_g / E;
epsilon_micro = epsilon_g * 1e6;

Vout = GF * epsilon_g * 1e3;   % mV/V (half bridge)

%% ===================== FATIGUE SF =================================
XS = sfatigue / sigma;

%% ===================== FRACTURE SF ================================
F_K = 1.12;
K_I = F_K * sigma * sqrt(pi * a);
XK = KIC / K_I;

%% ===================== DEFLECTION =================================
deflection = (F_handle * L^3) / (3 * E * I);

%% ===================== PRINT RESULTS ===============================
fprintf('\n===== FINAL RESULTS (%s) =====\n\n', material_name);

fprintf('E = %.1f Msi\n', E/1e6);
fprintf('su = %.1f ksi\n', su/1e3);
fprintf('sfatigue = %.1f ksi\n', sfatigue/1e3);
fprintf('KIC = %.1f ksi*sqrt(in)\n\n', KIC/1e3);

fprintf('Geometry: h = %.3f in, b = %.3f in\n', h, b);
fprintf('Moment of inertia I = %.5f in^4\n\n', I);

fprintf('Stress = %.2f ksi\n', sigma/1e3);
fprintf('Gauge strain (full bridge) = %.1f microstrain\n', epsilon_micro);
fprintf('Output = %.4f mV/V (req %.1f)\n', Vout, V_required);
fprintf('Deflection = %.5f in\n\n', deflection);

fprintf('Static SF X0 = %.2f (req %.1f)\n', X0, X0_req);
fprintf('Fatigue SF XS = %.2f (req %.1f)\n', XS, XS_req);
fprintf('Fracture SF XK = %.2f (req %.1f)\n\n', XK, XK_req);

if X0 >= X0_req && XS >= XS_req && XK >= XK_req && Vout >= 1 && deflection <= L/50
    fprintf('ALL CRITERIA MET: YES\n');
else
    fprintf('ALL CRITERIA MET: NO\n');
end
```

This code let me input the parameters of the chosen design and tweak it, while adding extra condtions, such as keeping handle deflection less than 2% of the length from the center of the drive to the end where load is applied.

---


#### Material Selection and Strain Gauge for Sensitivity Requirement

The selected material for the final design was **AISI A2 Tool Steel**. This material is more on the brittle side than the other aluminum or titanium options shown, and I picked it scepcifically for its high stiffness, as I wanted to reduce deformation as much as possible. Additionally, I felt that this would be a more reasonable material overall, due to a relative lower cost compared to the aluminum and titanium options.

<pre style="color:#9199cc;">
Mechanical properties used in analysis:
- Young’s modulus: **E = 30.3 Msi**  
- Ultimate strength: **σᵤ = 261 ksi**  
- Fatigue strength: **σ_f = 88.2 ksi**  
- Fracture toughness: **Kᴵᶜ = 17.6 ksi√in**
</pre>

Due to the high stiffness, I decided that the strain gauge itself should be a full bridge strain gauge, as this would amplify the voltage output to allow for the required 1 mV/V output. Four identical linear gauges can be installed on a single face of the wrench to form a **full Wheatstone bridge** configuration. The selected gauge type has a carrier size of **7.6 mm × 5.8 mm (≈0.3 in x 0.23 in)** , so four of these in the required configuration would fit on the side of the beam, that has a length under loading of **16 in**, and a height of **0.7 in**.

The gauge location is **1.0 in** from the center of the drive.

The full-bridge output was calculated using:

> <span style="color:#93B596;">V_out = *GF* · *ε* · *10³*  *(mV/V)*</span>
>
> <span style="color:#93B596;">Where:</span>
> <span style="color:#93B596;">- Gauge factor **GF = 2**</span><br>
> <span style="color:#93B596;">- ε is the strain at the gauge location</span>

The combination of this material's stiffness, and the full bridge strain gauge allow allow deformation to be kept on the lower side while still having the required output of **1.0 mV/V**

---

After this full process of iteration and tweaking, this was my final design:

<pre style="color:#9199cc;">
===== FINAL RESULTS (AISI A2 Tool Steel) =====

E = 30.3 Msi
su = 296.0 ksi
sfatigue = 88.2 ksi
KIC = 17.6 ksi*sqrt(in)

Geometry: h = 0.500 in, b = 0.700 in
Moment of inertia I = 0.00729 in^4

Stress = 20.57 ksi
Gauge strain (full bridge) = 636.5 microstrain
Output = 1.2730 mV/V (req 1.0)
Deflection = 0.23174 in

Static SF X0 = 14.39 (req 4.0)
Fatigue SF XS = 4.29 (req 1.5)
Fracture SF XK = 2.15 (req 2.0)

ALL CRITERIA MET: YES
</pre>

---


#### CAD Model and Dimensions

The torque wrench was modeled in **Fusion 360**. Below is a picture of the model istelf, and its relevant dimensions. To better resemble a real torque wrench, I filleted the drive with fillets of **0.05 in**. 

These dimensions were used directly in both the analytical calculations and the finite element model.

<img src="{{ '/assets/CAD.png' | relative_url }}"
     alt="CAD model"
     class="project-image">

<img src="{{ '/assets/CAD dimension.png' | relative_url }}"
     alt="Dimensions"
     class="project-image">

---

## <span style="color:#A7BADB;">FEM Analysis in ANSYS</span>

---

#### Loads and Boundary Conditions

The applied loading corresponds to a torque of **600 in-lbf**, modeled in the FEM as an equivalent force applied at the end of the handle. The square drive end of the wrench was fully constrained in displacement.

This loading configuration produces bending about a single neutral axis, consistent with the assumptions used in the analytical beam-bending calculations.

<img src="{{ '/assets/redesign_first_mesh/force_applied.png' | relative_url }}"
     alt="Loads and boundary conditions diagram"
     class="project-image">

<img src="{{ '/assets/redesign_first_mesh/clamped_drive.png' | relative_url }}"
     alt="Loads and boundary conditions diagram"
     class="project-image">

---


#### Mesh

I started with a Multizone mech that had **0.25 in** elements. After getting a set of measurements from it, I refined the mesh to have **0.1** in elements

First Mesh:
<img src="{{ '/assets/redesign_first_mesh/mesh_1_0.25_in.png' | relative_url }}"
     alt="Loads and boundary conditions diagram"
     class="project-image">

Refined Mesh:
<img src="{{ '/assets/2nd_mesh/new_mesh_0.1 in.png' | relative_url }}"
     alt="Loads and boundary conditions diagram"
     class="project-image">

---

#### Finite Element Analysis – Principal Stress

Contour plots for principal stresses:

Original mesh (**0.25 in** elements)
<img src="{{ '/assets/redesign_first_mesh/old_mesh_principal.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">

Refined mesh (**0.1 in** elements)
<img src="{{ '/assets/redesign_first_mesh/new_mesh_principal_contours.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">

---

#### Finite Element Analysis – Deflection at Load Point

Max Deflection at end of handle where loand is applied

Original mesh (**0.25 in** elements)
<img src="{{ '/assets/redesign_first_mesh/take2-deformation.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">
Deflection = **0.2803 in**

Refined mesh (**0.1 in** elements)
<img src="{{ '/assets/2nd_mesh/new_mesd_deformation.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">
---

#### Finite Element Analysis – Strain Results

Original mesh (**0.25 in** elements)
<img src="{{ '/assets/redesign_first_mesh/normal strain.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">

Refined mesh (**0.1 in** elements)
<img src="{{ '/assets/redesign_first_mesh/new_mesh_strain_contours.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">


###### Strain at gauge (1.0 in from drive center:)

<img src="{{ '/assets/redesign_first_mesh/strain_at_gauge_6.3532e-4.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">

- Original mesh (**0.25 in** elements): strain = 635.32 microstrain  
- Refined mesh (**0.1 in** elements): strain = 636.64 microstrain

---

#### Torque Wrench Sensitivity (From FEM Strain Results)

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

#### Finite Element Analysis – Normal Stress 

Original mesh (**0.25 in** elements)
<img src="{{ '/assets/images/normal_stress.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">

Refined mesh (**0.1 in** elements)
<img src="{{ '/assets/images/new_mesh_stress_max.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">
     
---

#### Results Summary (FEM Values)

Key results obtained from finite element analysis:

- Maximum normal stress: **___ ksi**  
- Load-point deflection: **___ in**  
- Strain at gauge location: **___ με**  
- Full-bridge output sensitivity: **___ mV/V**

All project performance requirements were satisfied.
---

#### Stress Concentration Considerations

New Geometry:
<img src="{{ '/assets/images/filet_wide_top.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">

Stress Analysis:
<img src="{{ '/assets/images/filet_wide_top_stress.png' | relative_url }}"
     alt="Normal strain contour plot"
     class="project-image">

---

## <span style="color:#A7BADB;">Conclusions and Reflections</span>

---

This project demonstrated the integration of mechanics of materials theory with finite element modeling and strain-based instrumentation.  
Strong agreement between analytical predictions and FEM results increased confidence in the final design.

Through this process, I gained experience balancing stiffness, strain sensitivity, and structural safety while translating theoretical requirements into a physically realizable mechanical instrument. Future work could include experimental calibration, electronics integration, and optimization of gauge placement for noise reduction.
