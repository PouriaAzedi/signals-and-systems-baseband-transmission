# signals-and-systems-baseband-transmission
A Python-based baseband digital communication chain using a real voice recording, covering quantization, μ-law companding, line coding, AWGN, matched filtering, and signal reconstruction.
# Signals & Systems — Baseband Digital Communication Chain

A Python-based implementation of a complete **baseband digital communication chain**, developed as a fourth-semester Signals & Systems course project in Electrical Engineering.

The project starts with a **real self-recorded voice signal** and processes it through quantization, digital encoding, line coding, a noisy AWGN channel, matched-filter reception, and final signal reconstruction.

## Project Overview

The complete communication pipeline is:

```text
Voice Recording
      ↓
Signal Acquisition & Preprocessing
      ↓
Uniform / μ-Law Quantization
      ↓
Bit Stream Generation
      ↓
Line Coding
      ↓
AWGN Channel
      ↓
Matched Filtering & Detection
      ↓
Signal Reconstruction
      ↓
Reconstructed Audio
```

The main objective is to implement each stage from first principles and evaluate how quantization, coding, channel noise, and receiver design affect the transmitted signal and the final reconstructed audio.

---

## Features

### 1. Signal Acquisition & Preprocessing

* Real self-recorded voice signal
* Mono audio processing
* Sampling frequency: **16 kHz**
* 16-bit PCM input
* Amplitude normalization to `[-1, 1]`
* Signal preparation for subsequent processing stages

### 2. Uniform Quantization

Uniform mid-rise quantization is implemented for:

* **8-bit**
* **4-bit**
* **2-bit**

For each configuration, the project calculates:

* Mean Squared Error (MSE)
* Signal-to-Quantization-Noise Ratio (SQNR)
* Quantization error
* Error histogram
* Reconstructed audio

The measured results are compared with the expected behavior of an ideal quantizer and interpreted in the context of a real speech signal.

### 3. μ-Law Companding

The project implements μ-law compression and its exact inverse using:

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

The performance of μ-law companding is evaluated at 8, 4, and 2 bits and compared against conventional uniform quantization.

This allows the effect of companding on low-amplitude speech components to be investigated.

### 4. Bit Stream Generation

Quantized sample indices are converted into a serial binary stream using:

* Natural binary coding
* MSB-first ordering
* Exact inverse conversion

The implementation includes a round-trip verification to ensure that:

```text
Sample Indices → Bits → Sample Indices
```

produces an exact reconstruction.

For the selected 8-bit configuration with a sampling frequency of 16 kHz:

```text
Rb = 128 kbit/s
```

### 5. Line Coding

Three polar line-coding schemes are implemented:

* **Polar NRZ**
* **Polar RZ**
* **Manchester**

For each coding scheme, the project analyzes:

* Time-domain waveform
* Power Spectral Density (PSD)
* Approximate bandwidth
* DC component

The analysis also demonstrates the expected bandwidth relationship between NRZ and RZ signaling and the near-zero DC characteristic of Manchester coding.

### 6. AWGN Channel

The transmitted line-coded signals are passed through an Additive White Gaussian Noise (AWGN) channel at:

```text
Eb/N0 = {-5, 0, 5, 10, 15, 20} dB
```

The bit energy is calculated separately for each line code to ensure that the simulated noise level corresponds correctly to the selected `Eb/N0`.

### 7. Matched Filtering & Detection

A matched filter is implemented using the time-reversed transmit pulse:

```text
h(t) = p(Tb - t)
```

The receiver then:

1. Convolves the received signal with the matched-filter response.
2. Samples at the appropriate decision instant.
3. Applies a zero-threshold detector.
4. Calculates the resulting BER.

The simulated BER is compared with the theoretical binary antipodal signaling performance:

```text
BER = Q(√(2Eb/N0))
```

### 8. End-to-End Simulation

All blocks are connected into a single communication pipeline:

```text
Audio → Quantization → Bits → Line Coding
      → AWGN → Matched Filter → Detection
      → Reconstructed Audio
```

Multiple combinations of quantization depth, line coding, and `Eb/N0` can be evaluated.

The resulting `.wav` files allow the effect of transmission quality to be evaluated not only numerically, but also by listening to the reconstructed speech.

---

## Repository Structure

```text
.
├── README.md
├── src/
│   ├── quantization.py
│   ├── mulaw.py
│   ├── bitstream.py
│   ├── line_coding.py
│   ├── awgn.py
│   ├── matched_filter.py
│   └── pipeline.py
│
├── figs/
│   ├── quantization/
│   ├── line_coding/
│   └── ber/
│
├── outputs/
│   ├── quantized/
│   └── reconstructed/
│
├── requirements.txt
└── .gitignore
```

> The exact file structure may vary depending on the organization of the source code.

---

## Requirements

The project uses Python with the following libraries:

```text
numpy
scipy
matplotlib
```

Install the dependencies with:

```bash
pip install -r requirements.txt
```

or:

```bash
pip install numpy scipy matplotlib
```

No dedicated communications toolbox is required. The main signal-processing and communication blocks are implemented directly using Python, NumPy, and SciPy.

---

## Why a Real Voice Signal?

Unlike an ideal sinusoidal test signal, real speech contains:

* Silence and pauses
* Breathing noise
* Low-amplitude components
* Strong amplitude variations
* Different distributions of vowels and consonants

These characteristics make the recording useful for studying the practical behavior of quantization and digital transmission.

In particular, they help demonstrate why the performance of a real speech signal can differ from simplified theoretical quantization estimates and why μ-law companding becomes particularly useful at lower bit depths.

---

## Results & Visualization

The project generates visualizations including:

* Quantization error histograms
* Original and reconstructed signals
* Line-coded waveforms
* Power Spectral Density plots
* BER versus `Eb/N0`
* Simulated versus theoretical BER

The generated audio outputs can also be used to directly compare the perceptual effect of different quantization and channel conditions.

---

## Phase 2

This repository represents **Phase 1** of the project.

Phase 2 extends the system toward a complete digital modem, including:

* BPSK
* QPSK
* 16-QAM
* 64-QAM
* Pulse shaping
* Multipath channel modeling
* Carrier frequency and phase offsets
* AWGN
* Carrier recovery
* Digital receiver processing

The bit stream generated in Phase 1 serves as the input to the modulation stage.

---

## Course Context

**Course:** Signals & Systems
**Program:** Electrical Engineering
**Semester:** 4th Semester
**Language:** Python

---

## Author

**Pouria Azedi**

Electrical Engineering Student
Shahid Beheshti University

---

## Keywords

`Python` `Signals and Systems` `Digital Communications` `Signal Processing` `Baseband Transmission` `Quantization` `μ-Law` `Line Coding` `AWGN` `Matched Filter` `BER`
