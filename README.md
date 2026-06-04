# Speech-QC

# GLAD-MHG Dynamic Survey Pipeline & QA Matrix Architect

A high-fidelity, interactive visualization and emulator engineered for the **GLAD-MHG** cohort speech-omics protocol. This tool maps out participant workflow pathways, client-side digital signal processing (DSP) gates, and dual-tier logic branching inside Qualtrics.

---

## 🧬 Framework & Pipeline Overview

The application couples a **Live Participant Emulator** with a real-time updating **Dynamic Procedural Logic Map (SVG)** to trace how participants navigate clinical vocal collection assets based on their profile data and acoustic environment.

### 1. Tier 1: Written MCQ Logic
* **Targeting Filter Gating:** Configured via in-page display logic. 
* **Bypass Execution:** If a participant selects **"NO"** on the filter question, the pipeline dynamically skips the **Speech Task Q3** block and its corresponding recovery loops, seamlessly advancing them to Task 4.

### 2. Tier 2: Acoustic Calibration Check (`glad-audio-qc-v2.js`)
* **Confined Retry Loop:** If the participant's acoustic environment fails validation parameters, navigation is strictly locked. The loopback routine keeps the user inside Tier 2 calibration, preventing accidental rollback to the Tier 1 written baseline.
* **Signal-to-Noise Ratio (SNR) Threshold:** 
  $$\text{SNR} = \text{Speech Peak (dBFS)} - \text{Ambient Noise Floor (dBFS)}$$
  * **Pass Condition:** Mean $\text{SNR} \ge 20\text{ dB}$.
  * **Fail Condition:** $\text{SNR} < 20\text{ dB}$ sets `qc_pass = "0"`, hides the native Qualtrics navigation buttons, and forces an environmental calibration retry.
* **Waveform Distortion Filter:** Monitors digital clipping saturation. Saturated sample frames exceeding a $2\%$ threshold block progression to ensure high-fidelity long-read speech biomarkers.

### 3. Core Speech Funnel & Voice Activity Detection (VAD)
* **Dual-Gate Recovery Loops:** Each active question tests total speech length via client-side VAD metrics.
* **Pass Gate:** Vocal duration $\ge 60\text{ seconds}$ unblocks the forward path cleanly.
* **Recovery Gate:** Vocal duration $< 60\text{ seconds}$ forces an branch-override, loading an immediate follow-up block to collect additional speech payload data.

---

## 📱 Cross-Platform QA Target Matrix (2026 Baseline)

To ensure high-throughput genomic data integration and digital phenotyping fidelity, this protocol targets a comprehensive validation matrix across major operating systems, browser configurations, and peripheral hardware.

| Priority | OS Environment | Browser Engine | Audio / Mic Peripheral | QA Verification Target |
| :--- | :--- | :--- | :--- | :--- |
| **High** | Windows 11 (24H2) | Google Chrome | Native Built-in Mic | Baseline Chromium audio processing thread. |
| **High** | Windows 11 (24H2) | Microsoft Edge | USB Headset Mic | Enterprise default browser audio driver handling. |
| **High** | macOS 16 | Apple Safari | Native Built-in Mic | Native WebKit MediaRecorder API compatibility. |
| **High** | macOS 16 | Mozilla Firefox | AirPods Pro (Bluetooth) | Bluetooth codec handover & Firefox media permission state persistence. |
| **High** | iOS 20 | Apple Safari | Native iPhone Mic | Mobile baseline Web Audio context under system gain controls. |
| **High** | iOS 20 | **Apple Safari** | **AirPods Pro (Bluetooth)** | Bluetooth internal hardware DSP/noise-canceling interference. |
| **High** | Android 17 | Google Chrome | Native Mobile Mic | Android system-level audio layer compatibility. |
| **Medium** | iOS 20 | **DuckDuckGo** | **AirPods Pro (Bluetooth)** | WebKit-wrapped privacy sandbox restriction handling. |
| **Medium** | Android 17 | **DuckDuckGo** | Native Mobile Mic | Chromium-based WebView privacy permission model. |
| **Medium** | iPadOS 20 | Apple Safari | Native / AirPods Pro | Screen asset scaling and hardware permission preservation. |
| **Low** | Ubuntu 26.04 LTS | Mozilla Firefox | USB System Headset | Linux Audio Worklet / PipeWire engine confirmation. |

---

## 🛠️ Critical Validation Test Vectors

During study pilot testing, verify these high-risk user path behaviors:
1. **Low Battery Throttling:** Validate recording stability on iOS/Android at $<20\%$ battery when the OS aggressively cuts thread power to background `AudioContext` structures.
2. **Mid-Session Hardware Handoff:** Connect AirPods Pro *during* the active 10-second calibration check to confirm browser thread switching without pipeline failure.
3. **Network Constraints:** Test the engine under simulated "Slow 3G" network throttle states to guarantee async scripts (`addModule`) initialize cleanly without timing out the Qualtrics layout.
