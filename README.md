# OculoSync
**Dynamic Cumulative Visual Allostatic Load Monitor**

> **OculoSync** is a proactive, hardware-agnostic clinical-grade digital ergonomics system. It leverages advanced computer vision and time-series machine learning to calculate real-time cumulative ocular stress, preventing acute ciliary muscle spasms and dry eye syndrome before physiological failure occurs.

---

## The Problem
Continuous visual stress is invisible until damage appears. Professional developers and designers endure prolonged screen exposure, leading directly to digital eye strain. Current solutions rely on static, time-based interventions (e.g., the 20-20-20 rule) or primitive blink counting. They fail because they ignore highly granular, sub-pixel ocular distress indicators and only attempt to intervene *after* symptoms manifest.

## The Solution
OculoSync establishes a personalized biometric baseline and tracks sub-pixel ocular movements, blink quality, and environmental light sync via a standard 1080p webcam. It dynamically adapts the visual environment—triggering localized, OS-level screen interventions—the moment the user's biological metrics cross a critical stress threshold.

---

## Core Architecture

OculoSync operates on a 6-module pipeline engineered for biomedical precision:

### **Module 0: The BPC-Index (Biometric Precision Calibration)**
Standardizes tracking to the user’s unique ocular anatomy. Uses a Perspective-n-Point (PnP) algorithm to map corneal distance and records resting palpebral fissure height to generate a personalized baseline multiplier.

### **Module 1: Tear Film Evaporation Estimator**
Moves beyond simple blink counting. Extracts Palpebral Fissure Height to track *incomplete blinks* in real-time, calculating a continuous Tear Film Evaporation Risk score.

### **Module 2: The ML Brain (CVAL Integrator)**
A 1D-CNN time-series model that ingests continuous ocular metrics. It calculates the **Cumulative Visual Allostatic Load (CVAL)** over time $t$ using the following continuous integration:

$$L_{visual}(t) = \int_{0}^{t} \left( \alpha \cdot \Delta P(t) + \beta \cdot f_{blink}(t)^{-1} + \gamma \cdot \sigma_{saccade}(t) \right) dt$$

*(Where $$Delta P(t)$$ is pupillary fluctuation,  $$f_{blink}(t)$$ is full-blink frequency, and $$\sigma_{saccade}$$ is saccadic jitter. Constants are tuned via Module 0).*

### **Module 3: Micro-Saccadic Tremor (MST) Detection**
Detects involuntary eye jitters indicating extraocular muscle fatigue. Isolates the iris contour and applies Lucas-Kanade optical flow to track sub-pixel movements.

### **Module 4: Pupillary Light Reflex (PLR) vs. Screen Sync**
Prevents localized phototoxicity. Maps the average RGB/Luma values of the active screen window against webcam-detected pupil diameter. High variance logs a "Phototoxic Stress Event."

### **Module 5: Ambient Degradation Fallback (Guardrail)**
Calculates a real-time Confidence Interval (CI) based on ambient pixel lux. If CI drops below 80% (e.g., low light), OS-level screen adjustments halt to prevent erratic flashing, ensuring responsible and safe AI behavior.

---

## Technical Stack

* **Computer Vision:** MediaPipe (468-point Face Mesh), OpenCV (Lucas-Kanade Optical Flow)
* **Machine Learning & Math:** PyTorch (1D-CNN), NumPy, SciPy (Continuous Integration)
* **OS Integration:** `mss` (Active Window Luma Capture), Native OS display APIs
* **Frontend/Telemetry:** FastAPI, React, Recharts (Biometric Dashboard)
