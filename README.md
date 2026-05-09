# 🏭 Digital Twin & SCADA Monitoring PID Level Control

## 📌 Executive Summary
This project demonstrates a comprehensive **Industry 4.0** automated system focusing on Local PID Control and Remote SCADA Monitoring. It integrates a Digital Twin of a liquid processing tank with a Micro800 Programmable Logic Controller (PLC). The system utilizes a manually tuned **Reverse-Acting PID Control Loop** configured via Ladder Logic to govern the physical plant locally. Simultaneously, real-time data is acquired and visualized through a high-fidelity **Ignition SCADA** dashboard bridged by **Cogent DataHub** (OPC DA/OPC UA).

## 🛠️ Mechatronics System Architecture

### 1. Mechanical Component (The Plant)
* **Environment:** Factory I/O Digital Twin.
* **Process:** An integrating liquid tank process with an analog proportional fill valve ($0-10\text{V}$) and a manual discharge valve.
* **Dynamics:** Highly sensitive continuous accumulation process requiring precise throttling to prevent integral windup.

### 2. Electrical & Network Component (I/O & Middleware)
* **Inputs (Sensors):** Analog Level Sensor ($0-10\text{V}$), Local Setpoint Potentiometer.
* **Outputs (Actuators):** Proportional Fill Valve ($0-10\text{V}$).
* **Communication Protocol:** **OPC DA / OPC UA** managed by Cogent DataHub, bridging the local PLC memory variables to the Ignition SCADA network seamlessly for real-time monitoring.

### 3. Software & Control Component (Logic)
* **Controller:** Allen-Bradley Micro800 Series (Programmed via CCW).
* **Language:** Standardized Ladder Logic (LD).
* **Control Algorithm:** Ideal ISA PID Equation configured for Reverse Acting (Fill Valve):
  $$CV(t) = K_c \left[ e(t) + \frac{1}{T_i} \int e(t) dt + T_d \frac{de(t)}{dt} \right]$$
* **System State:** The plant is entirely controlled locally via the Factory I/O panel, while the Ignition SCADA acts as a primary visualization and monitoring node.

---

## 💻 Software Stack
| Software / Tool | Purpose in Project |
| :--- | :--- |
| **Connected Components Workbench (CCW)** | PLC Programming (Ladder Logic) & Manual PID Tuning. |
| **Factory I/O** | 3D Digital Twin simulation of the physical plant & Local Control Panel. |
| **Cogent DataHub** | Middleware acting as the OPC DA / OPC UA Server and Client bridge. |
| **Ignition SCADA Software** | Designing the HMI Dashboard for real-time remote monitoring and data visualization. |

---

## 📊 Performance & Results

The PID controller was manually tuned to accommodate the physical constraints of the tank (absence of continuous discharge). The tuning parameters (`My_PID_Gains: Kc, Ti, Td`) were optimized to throttle the proportional valve perfectly.

* **Steady-State Error:** $\approx 1\%$
* **Overshoot:** Eliminated through precise integral tuning.
* **Test Case Example:** 
  * Requested Setpoint ($SV$): **50%**
  * Final Stable Level ($PV$): **51%**

---
---
## 📺 System Demonstration (Video)
Click the image below to watch the full system operation (Digital Twin + PLC + SCADA Integration):

[![Watch the video](https://img.shields.io/badge/🎥_Watch_Video-Project_Demo-blue?style=for-the-badge&logo=googledrive)](https://drive.google.com/file/d/1cnnt3jwVg3fsus2xE2UFIwAklyTGS_nF/view?usp=sharing)

---
## 🗂️ Project Structure

```text
📦 Digital-Twin-SCADA-PID-Control
 ┣ 📂 CCW_PLC_Project
 ┃ ┣ 📜 Tank_Level_Control.ccwsln       # Micro800 Solution File
 ┃ ┗ 📂 Controller_Files                # POU, Ladder Logic, and Variable Databases
 ┣ 📂 Factory_IO_Scene
 ┃ ┗ 📜 Tank_PID_Scene.factoryio        # Digital Twin Environment Backup
 ┣ 📂 Ignition_SCADA_Backup
 ┃ ┣ 📜 Ignition_Tags.json              # Exported OPC UA Tags
 ┃ ┗ 📜 SCADA_Window_Backup.proj        # SCADA HMI Screen Backup
 ┣ 📂 Docs                              # Screenshots of the Project
 ┣ 📜 README.md                         # Project documentation
 ┗ 📜 .gitignore

```

---

## 🔍 Troubleshooting & Known Communication Constraints

During the development of the **Remote Control** functionality, an OPC UA communication constraint was identified where Ignition SCADA buttons/sliders failed to overwrite PLC tags (displaying a `Good_WritePending` status in the Vision Property Editor). This indicates that the write command is sent by SCADA but rejected/held by the PLC or Middleware.

For future scaling to full remote control, the following architectural checkpoints must be verified:

1. **Micro800 Variable Scoping (CCW):**
OPC UA protocols generally cannot overwrite Local Variables. Target tags (e.g., `SCADA_Start_PB`, `SCADA_Remote_Mode`) must be declared exclusively within the **Global Variables** database in CCW before building and downloading.
2. **Ignition Tag Security (Read/Write Access):**
When pulling tags from Cogent DataHub, Ignition may default to secure read-only modes. In the Tag Editor, the `Access Rights` under the Security section must be explicitly changed from `Read_Only` to `Read_Write`.
3. **Ignition Client Mode Execution:**
Write commands execute only when the SCADA project is running in **Preview Mode (Play)** or within a deployed Vision Client, and the Project Communication settings are explicitly set to `Comm Read/Write` (not Comm Read-Only).

---

*Designed & Developed by **Ezz El-Din Al-Muliki** - Mechatronics & Control Systems Engineer.*
