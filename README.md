# lox-ch4-rocket-model
Script Python completo che modella le prestazioni ideali del motore, calcolando la spinta (F), l'impulso specifico (Isp) e trovando il rapporto ottimale ossidante/combustibile (O/F). Modello termodinamico in Python per un motore a razzo a propellente liquido (LOX / Metano, la combinazione scelta per motori moderni come il VegaE) Giulio Galatro
## 📑 Table of Contents
1. [Project Overview](#-project-overview)
2. [Scientific & Thermodynamic Theory](#-scientific--thermodynamic-theory)
   - [1. Stoichiometry & The O/F Ratio](#1-stoichiometry--the-of-ratio)
   - [2. Combustion Chamber Properties](#2-combustion-chamber-properties)
   - [3. Characteristic Velocity ($c^*$)](#3-characteristic-velocity-c)
   - [4. Thrust Coefficient ($C_f$) & Nozzle Expansion](#4-thrust-coefficient-c_f--nozzle-expansion)
   - [5. Thrust ($F$) & Specific Impulse ($I_{sp}$)](#5-thrust-f--specific-impulse-i_sp)
3. [Code Architecture](#-code-architecture)
4. [Quick Execution (Google Colab)](#-quick-execution-google-colab)
5. [Local Installation & Usage](#-local-installation--usage)
6. [License](#-license)

---

## 📋 Project Overview

This script models a liquid rocket engine to simulate how system performance changes across a spectrum of oxidizer-to-fuel (**$O/F$**) ratios. By automating the sweep over multiple $O/F$ ratios, the model successfully identifies the optimal operating point that maximizes **Specific Impulse ($I_{sp}$)**—the primary efficiency metric for rocket propulsion.

---

## 🔬 Scientific & Thermodynamic Theory

### 1. Stoichiometry & The $O/F$ Ratio
The chemical reaction for the combustion of methane with pure oxygen is:
$$\text{CH}_4 + 2\text{O}_2 \rightarrow \text{CO}_2 + 2\text{H}_2\text{O}$$

Using the molar masses ($\text{CH}_4 \approx 16.04\text{ g/mol}$, $\text{O}_2 \approx 32.00\text{ g/mol}$), the ideal stoichiometric mass ratio is:
$$\left(\frac{O}{F}\right)_{\text{stoch}} = \frac{2 \times 32.00}{16.04} \approx 3.99$$

> **Engineering Insight:** In real rocket engines, operating precisely at the stoichiometric ratio yields excessively high temperatures that can melt the combustion chamber walls. Therefore, engines operate slightly **fuel-rich** (typically an $O/F$ between $3.2$ and $3.6$), trading peak flame temperature for structural longevity while maintaining optimal performance.

---

### 2. Combustion Chamber Properties
The model uses empirical approximations mapped from NASA's CEA (Chemical Equilibrium with Applications) data at high chamber pressures ($5\text{–}10\text{ MPa}$) to calculate:
* **Chamber Temperature ($T_c$):** Modeled quadratically to peak near the stoichiometric region.
* **Molecular Weight ($MW$):** Increases as the oxidizer fraction grows.
* **Ratio of Specific Heats ($\gamma = c_p/c_v$):** Fluctuates typically between $1.13$ and $1.17$ for carbon-oxygen-hydrogen combustion products.

---

### 3. Characteristic Velocity ($c^*$)
The characteristic velocity ($c^*$, pronounced "c-star") measures the energy-release efficiency of the combustion chamber independently of the nozzle geometry:
$$c^* = \frac{\sqrt{\gamma R_{\text{specific}} T_c}}{\gamma \left(\frac{2}{\gamma + 1}\right)^{\frac{\gamma + 1}{2(\gamma - 1)}}}$$
Where $R_{\text{specific}} = \frac{R_{\text{universal}}}{MW}$ is the specific gas constant.

---

### 4. Thrust Coefficient ($C_f$) & Nozzle Expansion
The thrust coefficient accounts for the gas expansion through a converging-diverging (De Laval) nozzle and the pressure differential with the ambient atmosphere:
$$C_f = \sqrt{ \frac{2\gamma^2}{\gamma - 1} \left(\frac{2}{\gamma + 1}\right)^{\frac{\gamma + 1}{\gamma - 1}} \left[ 1 - \left(\frac{p_e}{p_c}\right)^{\frac{\gamma - 1}{\gamma}} \right] } + \left(\frac{p_e - p_a}{p_c}\right)\varepsilon$$
* $p_c$: Chamber pressure
* $p_e$: Nozzle exit pressure
* $p_a$: Ambient pressure
* $\varepsilon = \frac{A_e}{A_t}$: Nozzle expansion ratio (Exit Area / Throat Area)

---

### 5. Thrust ($F$) & Specific Impulse ($I_{sp}$)
* **Total Thrust ($F$):**
  $$F = \dot{m} \cdot c^* \cdot C_f$$
  where $\dot{m}$ is the total propellant mass flow rate.
* **Specific Impulse ($I_{sp}$):**
  $$I_{sp} = \frac{F}{\dot{m} \cdot g_0} = \frac{c^* \cdot C_f}{g_0}$$
  where $g_0 = 9.80665\text{ m/s}^2$ is standard Earth gravity. $I_{sp}$ represents the effective "fuel economy" of the engine, measured in seconds.

---

## 🛠️ Code Architecture

The script relies on an object-oriented Python structure (`LOXCH4EngineModel` class):
1. **`__init__`**: Sets up baseline geometry and operational inputs ($p_c, p_e, p_a, A_t, \varepsilon, \dot{m}$).
2. **`lox_ch4_properties(of_ratio)`**: Returns $T_c$, $MW$, and $\gamma$ for any given $O/F$ ratio.
3. **`calculate_performance(of_ratio)`**: Executes the core thermodynamic equations to return $c^*$, $C_f$, Thrust, and $I_{sp}$.
4. **`optimize_of(...)`**: Scans a range of $O/F$ ratios using `numpy.linspace` to pinpoint the peak $I_{sp}$.

---

## 📊 Quick Execution (Google Colab)

Run this simulation instantly in your browser without local installation:

👉 **[Open Model in Google Colab](https://colab.research.google.com/)** *(Upload or paste the script into a new notebook)*
