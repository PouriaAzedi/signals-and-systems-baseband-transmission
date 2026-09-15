# Signals & Systems — Baseband Digital Communication

A Python-based implementation of a complete **baseband digital communication chain**, developed as a fourth-semester **Signals & Systems course project** in Electrical Engineering.

The project starts from a **real self-recorded voice signal** and processes it through quantization, μ-law companding, digital bit-stream generation, line coding, an AWGN channel, matched-filter detection, and final audio reconstruction.

The main goal is to implement and analyze each stage of the communication system using fundamental signal-processing concepts and Python.

---

## 📡 Communication Pipeline

```text
Voice Recording
      │
      ▼
Signal Acquisition & Preprocessing
      │
      ▼
Uniform / μ-Law Quantization
      │
      ▼
Bit Stream Generation
      │
      ▼
Line Coding
      │
      ▼
AWGN Channel
      │
      ▼
Matched Filtering & Detection
      │
      ▼
Bit Reconstruction
      │
      ▼
Audio Reconstruction
```

---

## 🔬 Project Sections

### 1. Signal Acquisition & Preprocessing

A real voice recording is used as the input signal rather than a synthetic test tone.

The signal is processed to satisfy the required specifications:

* Sampling frequency: **16 kHz**
* Duration: **12 seconds**
* Mono audio
* Amplitude normalized to `[-1, 1]`
* 16-bit PCM WAV output

The resulting signal contains **192,000 samples** and serves as the reference signal for all subsequent measurements.

---

### 2. Uniform Quantization

A **mid-rise uniform quantizer** is implemented over the range `[-1, 1]`.

Three quantization depths are evaluated:

* **8 bits**
* **4 bits**
* **2 bits**

For each configuration, the following are calculated:

* Mean Squared Error (MSE)
* Signal-to-Quantization-Noise Ratio (SQNR)
* Quantization error
* Error histogram
* Reconstructed audio

#### Measured Results

| Bits |          MSE |      SQNR |
| ---: | -----------: | --------: |
|    8 | 7.199 × 10⁻⁶ |  25.52 dB |
|    4 | 2.595 × 10⁻³ |  -0.04 dB |
|    2 | 5.265 × 10⁻² | -13.12 dB |

The results demonstrate how strongly low-resolution quantization affects a real speech signal.

---

### 3. μ-Law Companding

To improve the representation of low-amplitude components, **μ-law companding** is implemented with:

```text
μ = 255
```

The complete process is:

```text
Input Signal
     ↓
μ-Law Compression
     ↓
Uniform Quantization
     ↓
μ-Law Expansion
     ↓
Reconstructed Signal
```

The implementation includes both the compression function and its exact inverse.

The inverse transformation was numerically verified with a maximum reconstruction error of approximately:

```text
8.88 × 10⁻¹⁶
```

#### Uniform vs. μ-Law SQNR

| Bits | Uniform SQNR | μ-Law SQNR | Improvement |
| ---: | -----------: | ---------: | ----------: |
|    8 |     25.52 dB |   37.72 dB |   +12.20 dB |
|    4 |     -0.04 dB |   13.47 dB |   +13.51 dB |
|    2 |    -13.12 dB |   -0.47 dB |   +12.64 dB |

These results show the advantage of companding for the tested voice signal, particularly when the available quantization resolution is limited.

---

### 4. Bit Stream Generation

The quantized sample indices are converted into a serial binary stream using:

* Natural binary coding
* MSB-first ordering
* Exact inverse conversion

The bitstream generation process was verified using a complete round-trip test:

```text
Quantized Indices
       ↓
     Bits
       ↓
Quantized Indices
```

The reconstruction error of the round-trip test was:

```text
0.0
```

For the selected **8-bit** configuration and `16 kHz` sampling rate:

```text
Bit Rate = 128 kbit/s
```

The resulting voice signal contains:

```text
1,536,000 bits
```

---

### 5. Line Coding

Three polar line-coding techniques are implemented:

* **Polar NRZ**
* **Polar RZ**
* **Manchester**

For each scheme, the project generates:

* Time-domain waveform
* Power Spectral Density (PSD)
* Approximate bandwidth
* DC-component analysis

The first 20 bits are visualized in the generated waveform figure, while the PSD is estimated using **Welch's method**.

#### Measured Characteristics

| Line Code  | Approx. Bandwidth |    DC Ratio |
| ---------- | ----------------: | ----------: |
| Polar NRZ  |            17 kHz |     0.00962 |
| Polar RZ   |         128.5 kHz |     0.00437 |
| Manchester |         128.5 kHz | 5.66 × 10⁻⁷ |

The Manchester implementation exhibits an effectively negligible DC component.

---

### 6. AWGN Channel

The line-coded signals are transmitted through an **Additive White Gaussian Noise (AWGN)** channel.

The following `Eb/N0` values are evaluated:

```text
-5, 0, 5, 10, 15, 20 dB
```

The energy per bit is calculated according to the pulse shape of each line code, allowing the noise level to be calibrated independently for:

* Polar NRZ
* Polar RZ
* Manchester

This provides a consistent basis for comparing the different signaling schemes.

---

### 7. Matched Filtering & Detection

At the receiver, a matched filter is implemented using the time-reversed transmit pulse.

The received waveform is:

1. Convolved with the matched-filter response
2. Sampled at the optimal decision instant
3. Detected using a zero threshold
4. Compared against the transmitted bit sequence

The resulting BER is then evaluated over multiple `Eb/N0` values.

#### BER Results

For a representative 8-bit Polar NRZ transmission:

| Eb/N0 | Simulated BER | Theoretical BER |
| ----: | ------------: | --------------: |
| -5 dB |       0.21317 |         0.21323 |
|  0 dB |       0.07895 |         0.07865 |
|  5 dB |       0.00596 |         0.00595 |
| 10 dB |   3.26 × 10⁻⁶ |     3.87 × 10⁻⁶ |
| 15 dB |            ~0 |    9.12 × 10⁻¹⁶ |
| 20 dB |            ~0 |    1.04 × 10⁻⁴⁵ |

The simulated BER closely follows the theoretical binary antipodal signaling curve over the practical simulation range.

---

## 🔗 End-to-End Simulation

All implemented blocks are finally connected into a single end-to-end communication system:

```text
Voice
  ↓
μ-Law Companding
  ↓
Quantization
  ↓
Binary Encoding
  ↓
Line Coding
  ↓
AWGN Channel
  ↓
Matched Filter
  ↓
Bit Detection
  ↓
Bit-to-Index Reconstruction
  ↓
μ-Law Expansion
  ↓
Reconstructed Voice
```

Several combinations of quantization depth, `Eb/N0`, and line-coding technique are evaluated.

### Example Configurations

| Quantization | Eb/N0 | Line Code  |     BER |
| -----------: | ----: | ---------- | ------: |
|        8-bit | 20 dB | Polar NRZ  |       0 |
|        8-bit |  5 dB | Polar NRZ  | 0.00596 |
|        4-bit | 20 dB | Manchester |       0 |
|        4-bit |  0 dB | Manchester | 0.07852 |
|        8-bit | -5 dB | Polar RZ   | 0.21319 |
|        2-bit | 20 dB | Polar NRZ  |       0 |

The reconstructed WAV files allow the effect of quantization and channel noise to be evaluated both numerically and perceptually.

---

## 📊 Generated Results

The repository contains several generated visualizations, including:

* Uniform quantization error histograms
* Line-coding waveforms
* Power Spectral Density comparison
* Simulated vs. theoretical BER curve

Located in:

```text
figs/
```

The generated audio files and numerical report are stored in:

```text
outputs/
```

---

## 📁 Repository Structure

```text
Voiceproject/
│
├── README.md
│
├── main.py
├── newfile.py
│
├── s1_signal.py
├── s2_uniform_quant.py
├── s3_mulaw.py
├── s4_bitstream.py
├── s5_linecoding.py
├── s6_awgn.py
├── s7_matched_filter.py
├── s8_full_pipeline.py
│
├── figs/
│   ├── s2_hist_B2.png
│   ├── s2_hist_B4.png
│   ├── s2_hist_B8.png
│   ├── s5_waveforms.png
│   ├── s5_psd.png
│   └── s8_ber_curve.png
│
└── outputs/
    ├── report_data.json
    ├── section1_original.wav
    ├── s2_uniform_B2.wav
    ├── s2_uniform_B4.wav
    ├── s2_uniform_B8.wav
    ├── s3_mulaw_B2.wav
    ├── s3_mulaw_B4.wav
    ├── s3_mulaw_B8.wav
    └── section8_final_*.wav
```

---

## 🛠️ Technologies

* **Python**
* **NumPy**
* **SciPy**
* **Matplotlib**

Install the required dependencies with:

```bash
pip install numpy scipy matplotlib
```

No dedicated communications toolbox is required. The main signal-processing and communication blocks are implemented directly using Python, NumPy, and SciPy.

---

## ▶️ Running the Project

Clone the repository and install the required packages:

```bash
pip install numpy scipy matplotlib
```

Then run the main project script:

```bash
python newfile.py
```

The program performs the complete sequence of Sections 1–8 and generates the corresponding figures, reconstructed audio files, BER results, and numerical report.

---

## 🎯 Key Takeaways

This project demonstrates a complete baseband digital communication workflow while connecting theoretical Signals & Systems concepts to practical simulation.

The main observations include:

* Real speech behaves differently from idealized theoretical signals.
* Increasing quantization resolution significantly improves reconstruction quality.
* μ-law companding substantially improves SQNR for the tested voice signal.
* Different line codes produce significantly different spectral characteristics.
* Manchester signaling provides an effectively negligible DC component.
* Proper `Eb/N0` calibration must account for the pulse shape of each line code.
* Matched filtering enables reliable symbol detection in the presence of AWGN.
* Simulated BER closely follows the theoretical performance at moderate-to-high `Eb/N0`.

---

## 🚀 Future Work — Phase 2

This repository represents **Phase 1** of the project.

The next phase extends the system toward a complete digital modem, including:

* BPSK
* QPSK
* 16-QAM
* 64-QAM
* Pulse shaping
* Multipath channel modeling
* Carrier frequency offset
* Carrier phase offset
* AWGN
* Carrier recovery
* Digital receiver processing

The bitstream generated in this phase can serve as the input to the modulation and modem chain developed in Phase 2.

---

## 🎓 Academic Context

**Course:** Signals & Systems
**Program:** Electrical Engineering
**Semester:** 4th Semester
**Programming Language:** Python

---

## 👤 Author

**Pouria Azedi**

Electrical Engineering Student
Shahid Beheshti University

---

## 📌 Keywords

`Python` `Signals and Systems` `Digital Communications` `Signal Processing` `Baseband Transmission` `Quantization` `μ-Law` `Line Coding` `AWGN` `Matched Filter` `BER` `Electrical Engineering`
