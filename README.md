# Mohammad Badri Ahmadi

**Embedded systems & on-device AI.** I build the part of a machine that decides —
sensor fusion, Kalman filtering and small neural networks that run on a microcontroller
beside a live CAN stack, deterministic under load, on vehicles in the field.

My estimation methods are published in *IEEE Transactions on Neural Systems and
Rehabilitation Engineering* and the *Journal of Neuroscience Methods*. A decade spent
separating signal from noise — first in brains, now in machines.

`ESP32` · `CAN / CANopen / J1939` · `Bosch Rexroth RC5-6/40 (BODAS)` · `ifm CR710S / CODESYS`
· `C` · `Python` · `EKF` · `TensorFlow → MCU`

🌐 **[biss.qzz.io](https://biss.qzz.io)** · 📧 **[contact@biss.qzz.io](mailto:contact@biss.qzz.io)**

---

<div align="center">
  <table>
    <tr>
      <td align="center" width="50%">
        <img src="https://raw.githubusercontent.com/mhmmdbdrhmd/Data/main/gifs/ekf-tracking.gif" width="400" alt="Extended Kalman filter recovering truth from noisy measurements"/>
        <br/><sub><b>State estimation</b> — EKF, Joseph-form covariance, ±1σ band</sub>
      </td>
      <td align="center" width="50%">
        <img src="https://raw.githubusercontent.com/mhmmdbdrhmd/Data/main/gifs/can-stream.gif" width="400" alt="CAN bus frames decoded live"/>
        <br/><sub><b>CAN / CANopen</b> — acquisition and live decoding</sub>
      </td>
    </tr>
    <tr>
      <td align="center" width="50%">
        <img src="https://raw.githubusercontent.com/mhmmdbdrhmd/Data/main/gifs/mlp-inference.gif" width="400" alt="233-parameter MLP running inference on an ESP32"/>
        <br/><sub><b>On-device AI</b> — 233 parameters, 27–8–1, ≈4 ms on an ESP32</sub>
      </td>
      <td align="center" width="50%">
        <img src="https://raw.githubusercontent.com/mhmmdbdrhmd/Data/main/gifs/stationary-gate.gif" width="400" alt="Stop-signal gating a speed estimator"/>
        <br/><sub><b>Sensor fusion</b> — stop-signal gating, false positives ≈2%</sub>
      </td>
    </tr>
    <tr>
      <td align="center" width="50%">
        <img src="https://raw.githubusercontent.com/mhmmdbdrhmd/Data/main/gifs/zupt.gif" width="400" alt="Zero-velocity update bounding the covariance while parked"/>
        <br/><sub><b>Zero-velocity update</b> — parked wheels emit no ticks, so the covariance inflates unopposed. Shown in isolation; a slew cap handles position drift separately</sub>
      </td>
      <td align="center" width="50%">
        <img src="https://raw.githubusercontent.com/mhmmdbdrhmd/Data/main/gifs/parity.gif" width="400" alt="Embedded and host paths diverging when a feature buffer is mis-mapped"/>
        <br/><sub><b>Embedded/host parity</b> — replay the same bundle offline; a mis-mapped feature buffer shows up instantly</sub>
      </td>
    </tr>
  </table>
  <sub><i>Every frame above is computed, not drawn — the EKF, the network and the gate are real implementations.</i></sub>
</div>

---

## 🔧 Engineering Work

**On-device stationary detection for vehicle speed estimation**
A 233-parameter MLP (27–8–1) running in real time on an ESP32 alongside CAN acquisition,
producing a stop-signal that gates a downstream speed estimator. Causal multi-scale
rolling-standard-deviation features across two 6-axis CAN IMUs and a PLC-derived hitch angle.
**End-to-end feature construction plus forward pass in ≈4 ms.** Deployed via an SD-card model
bundle — thresholds, window sizes, feature ordering and weights change with no firmware
recompilation. Designed around minimising false positives; field false-positive time fraction
reduced from ~5% to ~2% through embedded/host parity analysis that isolated a feature-mapping
bug from model error.

**Steering angle from wheel encoders alone**
Recovering drawbar steering angle online from two wheel encoders, with the angle sensor used
strictly as ground truth — never as a filter input. The dominant error was integer tick
quantisation amplified by the ratio formula, so **the filtering moved ahead of the ratio**
onto the raw channels, where each signal is smooth and high-SNR. Causal cascade: Hampel spike
rejection → speed-adaptive per-wheel Kalman smoother → angle KF with ZUPT, Huber and
manoeuvre-gated measurement noise, a speed-scaled slew cap and a fixed-lag RTS smoother
(100 ms latency) → fixed nonlinear amplitude map. **RMSE 9.03 / 7.81 / 6.56°** across three
field recordings on one frozen parameter set, with the acceleration tail at or below the
reference signal's own. No-leakage is *proven*: a scramble test overwriting the reference
columns yields byte-identical output, and leave-one-recording-out confirms the amplitude
constants generalise.

**Bosch Rexroth RC5-6/40 ECU commissioning**
Portable PLC test bench around a Rexroth RC5-6/40 mobile controller — supply, ignition and
inhibit-pin wiring, CAN interfacing, full BODAS/HighTec build-and-flash validation. Quadrature
ABS wheel-speed acquisition in both trusted and memory-protected untrusted partitions, plus
dual-channel analog angle integration, verified against two independent CAN analysers with a
custom DBC database.

*Sanitised case studies available on request; full technical reports under NDA.*

---

## 📜 Peer-Reviewed Publications

**A Mixed Filtering Approach for Real-Time Seizure State Tracking Using Multi-Channel Electroencephalography Data**
*IEEE Transactions on Neural Systems and Rehabilitation Engineering*, vol. 29, pp. 2037–2045, 2021
[**DOI: 10.1109/TNSRE.2021.3113888**](https://doi.org/10.1109/TNSRE.2021.3113888)
Real-time seizure-state tracking from EEG with a minimal channel count.
*Methods: state-space framework, expectation maximisation, wrapper feature selection, mixed filtering.*

**An EEG-fNIRS Hybridization Technique in the Four-Class Classification of Alzheimer's Disease**
*Journal of Neuroscience Methods*, vol. 336, p. 108618, 2020
[**DOI: 10.1016/j.jneumeth.2020.108618**](https://doi.org/10.1016/j.jneumeth.2020.108618)
Multimodal fusion of EEG and fNIRS to separate healthy controls from staged Alzheimer's patients.
*Methods: multimodal feature fusion, wrapper feature selection, four-class classification.*

**Real-Time Seizure State Tracking Using Two Channels: A Mixed-Filter Approach**
*53rd Asilomar Conference on Signals, Systems, and Computers*, 2019, pp. 2033–2039
[**DOI: 10.1109/IEEECONF44664.2019.9048990**](https://doi.org/10.1109/IEEECONF44664.2019.9048990)
*Methods: mixed-filter approach, Kalman filtering, real-time state tracking, LDA.*

**Near-Perfect Neural Critic from Motor Cortical Activity Toward an Autonomously Updating Brain-Machine Interface**
*IEEE Engineering in Medicine and Biology Society (EMBC)*, 2018, pp. 73–76
[**DOI: 10.1109/EMBC.2018.8512274**](https://doi.org/10.1109/EMBC.2018.8512274)
*Methods: power spectral density, spike-field coherence, MLP, kNN, RBF-SVM, Gaussian processes.*

---

## 🏫 Education

<div align="center">

| | |
| :---: | :---: |
| [<img src="https://upload.wikimedia.org/wikipedia/commons/2/2a/University_of_Houston_Logo.svg" width="200" alt="University of Houston"/>](http://www.uh.edu) | [<img src="https://www.sharif.edu/documents/20124/0/SharifUniLogo+copy.png/55eb27b7-ff95-16f4-d1b1-802c72ab7d47?t=1650190956745" width="200" alt="Sharif University of Technology"/>](http://www.sharif.edu) |
| **University of Houston** <br>Doctoral studies, Biomedical Engineering<br>2016–2020 · GPA 4.0/4.0 | **Sharif University of Technology** <br>B.Sc. Chemical Engineering<br>2012–2016 |

</div>

---

<br><br>

<div align="center"><p align="center">
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    <a href="mailto:contact@biss.qzz.io" style="text-decoration: none;" alt="Email">
        <img src="https://github.com/mhmmdbdrhmd/Data/blob/main/Icons/ICON%20_Black%20-%20GMail.png" width="6%" />
    </a>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    <a href="https://github.com/mhmmdbdrhmd" style="text-decoration: none;" alt="GitHub">
        <img src="https://github.com/mhmmdbdrhmd/Data/blob/main/Icons/ICON%20_Black-%20Github.png" width="6%" />
    </a>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
    <a href="https://www.linkedin.com/in/mohamad-badri-ahmadi-aa2a1a8a" style="text-decoration: none;" alt="LinkedIn">
        <img src="https://github.com/mhmmdbdrhmd/Data/blob/main/Icons/ICON%20_Black%20-%20Linkding.png" width="6%" />
    </a>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://twitter.com/mhmmdbdrhmd" style="text-decoration: none;" alt="Twitter">
        <img src="https://github.com/mhmmdbdrhmd/Data/blob/main/Icons/ICON%20_Black%20-%20Twitter%20X.png" width="6%"/>
    </a>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  <a href="https://biss.qzz.io" style="text-decoration: none;" alt="Website">
        <img src="https://github.com/mhmmdbdrhmd/Data/blob/main/Icons/ICON%20_Black%20-%20Website.png" width="6%"/>
    </a>
    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</p></div>
