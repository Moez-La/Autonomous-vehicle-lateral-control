# Autonomous Vehicle Lateral Control & Safety Mechanism

A comprehensive autonomous vehicle control system featuring lateral control algorithms (Proportional, Pure Pursuit), ECU failure safety mechanisms, and functional safety analysis following **ISO 26262** standards. Developed as part of a collaborative engineering project (BEI) with **EasyMile**.

---

## 🎯 Project Overview

This project addresses **functional safety** in autonomous vehicle lateral control through:

- **Lateral Control Algorithms**: Implementation of Proportional and Pure Pursuit controllers for precise trajectory tracking
- **Safety Analysis**: Complete PHA (Preliminary Hazard Analysis) and HARA (Hazard Analysis and Risk Assessment) following ISO 26262
- **Safety Mechanisms**: ECU failure detection with automatic safe parking trajectory generation
- **Real-time Simulation**: Interactive PyQt6-based simulator with manual keyboard control and autonomous modes
- **Comprehensive Testing**: Unit tests with pytest validating controller performance across multiple scenarios

---

## 📹 Simulation Demo

![Autonomous Vehicle Simulation](Simulation.gif)

*Real-time demonstration of lateral control algorithms, manual keyboard control mode, and ECU failure safety mechanism with gradual deceleration and safe parking trajectory*

---

## 🛠️ Technical Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.12+ |
| **UI Framework** | PySide6 (Qt6) |
| **Visualization** | PyQtGraph (real-time plotting) |
| **Mathematics** | NumPy, WPILib (robotics) |
| **Testing** | Pytest |
| **Control Algorithms** | Proportional Control, Pure Pursuit |
| **Safety Standards** | ISO 26262, ISO 13849 |

---

## 📦 Project Structure
├── Autopilot/
│   └── autopilot.py              # Main autopilot logic with safety integration
├── IHM/
│   └── ihm.py                    # User Interface (PyQt6)
├── Lateral_control/
│   ├── proportional_control.py   # Proportional controller implementation
│   ├── pure_pursuit_control.py   # Pure Pursuit algorithm
│   ├── test_pure_pursuit.py      # Pytest unit tests
│   └── lateral_control_pure_pursuit_parameters.json  # Controller config
├── Model/
│   └── vehicle_model.py          # Vehicle dynamics model
├── Safety_mecanism/
│   ├── safety_mecanism.py        # ECU failure handling & safe parking
│   └── Alarme.wav                # Audio safety alert
├── Trajectory/
│   └── generate_trajectory.py    # Trajectory generation
├── Logs/
│   └── failure_log.txt           # Failure event logging
├── docs/
│   ├── Rapport_final_BEI_EM_2024_25.pdf               # Complete project report
│   ├── Analyse_PHA_HARA_Controle_Lateral.xlsx         # Risk analysis
│   ├── Architecture_UML_fonction_secu.pptx            # UML diagrams
│   └── UML_fonction_secu.mdj                          # StarUML model
├── main_IHM.py                   # Application entry point
├── requirements.txt              # Python dependencies
├── Simulation.gif                # Demo GIF
└── README.md

# How to Use the Simulator

---

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
git clone https://github.com/Moez-La/Autonomous-vehicle-lateral-control.git
cd Autonomous-vehicle-lateral-control

# Install dependencies (Python >= 3.12 required)
pip install -r requirements.txt
```

### Run the Simulator

```bash
python main_IHM.py
```

**Controls:**
- **Autonomous mode**: Vehicle follows predefined trajectory using lateral controller
- **Manual mode**: Use keyboard arrows to control the vehicle
- **ECU failure simulation**: Trigger safety mechanism to observe safe parking behavior

### Run Unit Tests

```bash
python3 -m pytest Lateral_control/test_pure_pursuit.py -v
```

---

## 📹 Simulation Demo

![Autonomous Vehicle Simulation](Simulation.gif)

*Real-time demonstration of lateral control algorithms (Proportional & Pure Pursuit), manual keyboard control mode, and ECU failure safety mechanism with gradual deceleration and safe parking trajectory*

---

## 🧠 Lateral Control Algorithms

### 1. Proportional Control

Simple and efficient control based on cross-track error:

δ = Kp × e
Where:
- `δ` = Steering angle
- `Kp` = Proportional gain
- `e` = Lateral error

**Advantages:**
- Simple implementation
- Low computational cost
- Fast response time

**Limitations:**
- Sensitive to speed variations
- May oscillate if gain is not well-tuned

---

### 2. Pure Pursuit Control

Geometric path-tracking algorithm using a lookahead point:

δ = arctan(2L sin(α) / ld)
ld = Kdd × Vf
Where:
- `L` = Vehicle wheelbase
- `α` = Angle to target point
- `ld` = Lookahead distance (dynamic based on speed)
- `Kdd` = Lookahead gain
- `Vf` = Forward velocity

**Advantages:**
- Good stability at moderate speeds
- Handles smooth curves well
- Adaptive to speed variations

**Implementation:**
- Dynamic lookahead distance based on vehicle speed
- Steering angle saturation (±30°)
- Minimum lookahead distance (1.0m) for low-speed stability

---

## 🛡️ Safety Mechanisms

### ECU Failure Safety Function (UC2)

Implemented safety mechanism activated when ECU failure is detected:

**1. Failure Detection**
- Monitors ECU status in real-time
- Triggers safety mode on failure detection

**2. Safe Parking Trajectory Generation**
- Calculates lateral offset trajectory (shift right)
- Applies perpendicular normal vector to current trajectory
- Ensures vehicle moves to safe zone

**3. Gradual Deceleration**
- Progressive speed reduction to avoid harsh braking
- Iterative velocity decrease until safety threshold
- Prevents sudden vehicle stops

**4. Controlled Stop**
- Autopilot follows modified trajectory
- Steering angle adjusted during deceleration phase
- Complete stop with final position logging

**5. Alert System**
- Audio alarm (Alarme.wav) activation
- Visual alerts in UI
- Failure event logged to `Logs/failure_log.txt`

---

## 📊 Functional Safety Analysis

### ISO 26262 Compliance

Complete safety analysis following automotive functional safety standards:

**Preliminary Hazard Analysis (PHA)**
- Identification of hazard sources affecting lateral control
- Severity and occurrence classification
- Prevention and mitigation strategies

**Hazard Analysis and Risk Assessment (HARA)**
- Systematic risk identification for each system component
- Risk evaluation based on:
  - **Severity** (S): Impact of hazard (collision, injury, deviation)
  - **Exposure** (E): Frequency of hazardous scenario
  - **Controllability** (C): Ability to manage the situation
- Definition of Safety Goals and mitigation measures

**Identified Critical Risks:**
- Collision with pedestrians (perception failure)
- Lane departure (trajectory tracking error)
- Loss of GPS signal (localization failure)
- ECU controller failure (lateral/longitudinal control)
- Emergency braking absence
- Road signalization misperception

**Safety Functions Defined:**
- UC1: Safety Mode (Perception Failure)
- UC2: Safety Mode (ECU Failure) ← **Implemented**
- UC3: Localization Continuity (GPS fallback)
- UC4: Safety Mode (Software Bug)
- UC5: Detection Availability (Backup ECU)
- UC6: Angular Position Availability (Redundant sensor)

📄 **Complete analysis available**: `docs/Analyse_PHA_HARA_Controle_Lateral.xlsx`

---

## ✅ Validation & Testing

### Test Coverage

Six comprehensive pytest tests validating Pure Pursuit controller:

1. **Straight Path Test**: Validates zero steering on aligned trajectory
2. **Sharp Turn Test**: Verifies proper steering angle on 90° turns (±30°)
3. **Empty Path Handling**: Tests graceful error handling (returns 0°)
4. **Low Speed Behavior**: Validates minimum lookahead distance (1.0m)
5. **Steering Saturation**: Confirms ±30° limit enforcement
6. **Off-Path Correction**: Tests strong correction on 1m lateral deviation

**Test Results:**
lateral_control/test_lateral_control.py::test_straight_path PASSED      [16%]
lateral_control/test_lateral_control.py::test_sharp_right_turn PASSED   [33%]
lateral_control/test_lateral_control.py::test_empty_path_handling PASSED[50%]
lateral_control/test_lateral_control.py::test_low_speed_behavior PASSED [66%]
lateral_control/test_lateral_control.py::test_steering_saturation PASSED[83%]
lateral_control/test_lateral_control.py::test_off_path_correction PASSED[100%]
============================== 6 passed in 0.21s ==============================

---

## 📂 Documentation

### Technical Documentation

- **[Final Report (PDF)](./docs/Rapport_final_BEI_EM_2024_25.pdf)** - Complete 24-page academic report covering:
  - State-of-the-art lateral control algorithms
  - PHA/HARA risk analysis methodology
  - Controller design and implementation
  - Safety function development
  - UML architecture and use cases
  - Validation results and recommendations

- **[PHA/HARA Risk Analysis (Excel)](./docs/Analyse_PHA_HARA_Controle_Lateral.xlsx)** - Detailed safety analysis tables:
  - Hazard scenarios and causes
  - Risk evaluation (Severity × Exposure × Controllability)
  - Safety goals and mitigation measures
  - Traceability matrix

- **[UML Architecture (PowerPoint)](./docs/Architecture_UML_fonction_secu.pptx)** - Safety function diagrams:
  - Use case diagrams (UC1-UC6)
  - Sequence diagrams (failure detection flow)
  - System architecture overview

- **[UML Model (StarUML)](./docs/UML_fonction_secu.mdj)** - Complete UML model files for further development

---

## 🎓 Learning Outcomes

This project demonstrates expertise in:

- **Autonomous Vehicle Control**: Implementation of industry-standard lateral control algorithms
- **Functional Safety**: Application of ISO 26262 methodology (PHA/HARA)
- **Real-time Systems**: Safety-critical embedded system design
- **Software Engineering**: Modular Python architecture, unit testing, documentation
- **Risk Analysis**: Systematic hazard identification and mitigation strategies
- **Human-Machine Interface**: Interactive simulation and visualization

---

## 🔗 Project Context

**Bureau d'Études Industriel (BEI)** - Collaborative Engineering Project  
**Partner**: EasyMile (Autonomous Vehicle Company)  
**Institution**: INP-ENSEEIHT Toulouse (3A ACISE)  
**Academic Year**: 2024-2025

**Objective**: Analyze and improve the functional safety of lateral control in autonomous cargo transport vehicles through risk analysis, controller design, and safety mechanism implementation.

---

## 👥 Authors

**Moez CHAGRAOUI** 
**Rayen YADIR** 
**Yassine ABDELILLAH** 
**Drissa SAGNON** 

*INP-ENSEEIHT - Collaborative Engineering Project (BEI) with EasyMile*

[LinkedIn - Moez Chagraoui](https://www.linkedin.com/in/moez-chagraoui) • [Email](mailto:moezchagraoui@gmail.com)

---

## 📝 License

MIT License - See LICENSE file for details

---

*Built with Python • PySide6 • ISO 26262 Functional Safety*


