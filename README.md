# 🛰️ Adaptive Coding & Modulation (ACM) — LDPC vs Polar over Rayleigh Fading

A Monte-Carlo communication-systems simulator that benchmarks **Adaptive Coding and Modulation (ACM)** against fixed-rate channel codes and uncoded transmission over a **Rayleigh fading channel with AWGN**. It implements **LDPC** and **Polar** codes at multiple rates, and an adaptive controller that switches code rate based on channel quality to maximize reliable throughput.

<p align="left">
  <img src="https://img.shields.io/badge/Python-3.8%2B-3776AB?logo=python&logoColor=white" alt="Python 3.8+">
  <img src="https://img.shields.io/badge/NumPy-scientific-013243?logo=numpy&logoColor=white" alt="NumPy">
  <img src="https://img.shields.io/badge/Matplotlib-plots-11557C?logo=python&logoColor=white" alt="Matplotlib">
  <img src="https://img.shields.io/badge/pyldpc-LDPC-4B8BBE" alt="pyldpc">
  <img src="https://img.shields.io/badge/domain-Wireless%20Comms-brightgreen" alt="Wireless Comms">
</p>

---

## 📡 What This Project Does

Modern wireless links (4G/5G/Wi-Fi) must stay reliable as channel conditions change. This simulator demonstrates **why adaptive coding wins**: instead of a single fixed code rate, it picks the best rate for the current Signal-to-Noise Ratio (SNR) — sending data aggressively when the channel is clean and conservatively when it's noisy.

The pipeline for each transmission:

```
random bits → channel encode (LDPC / Polar) → BPSK modulate
            → Rayleigh fading + AWGN → ZF equalize + soft LLRs
            → channel decode → count bit/block errors
```

It sweeps SNR from **0 → 12 dB** and, at each point, measures **BER**, **BLER**, and **throughput** for every scheme, then compares them.

---

## ✨ Features

- **Two modern code families** — LDPC (via `pyldpc`) and Polar codes, each at rates **1/3, 1/2, 3/4**
- **Adaptive controller (ACM)** — selects code rate from effective SNR to maximize throughput while holding error rates low
- **Realistic channel model** — Rayleigh fading + AWGN with proper noise-variance scaling
- **Soft-decision decoding** — Zero-Forcing equalization with matched-filter LLRs weighted by channel reliability `|h|²`
- **Monte-Carlo engine** — runs frames until a minimum block-error count for statistically meaningful curves
- **Baselines** — fixed-rate LDPC/Polar and uncoded BPSK for reference
- **8 publication-style plots** generated automatically

---

## 📊 Generated Results

Running the simulation populates the `results/` folder with 8 plots:

| Plot | Description |
| ---- | ----------- |
| `proposal_ber.png` | BER vs SNR — LDPC / Polar / Adaptive comparison |
| `ber_plot_adaptive.png` | Dedicated BER curve for the Adaptive (ACM) scheme |
| `proposal_throughput.png` | Throughput vs SNR (normalized & stepwise) |
| `bler_realistic.png` | Block Error Rate (BLER) for multi-rate codes |
| `ber_plot.png`, `ber_realistic.png` | Standard / legacy BER comparisons |
| `throughput_plot.png`, `throughput_realistic.png` | Standard / legacy throughput comparisons |

**Metrics explained:**
- **BER (Bit Error Rate)** — fraction of bits received incorrectly *(lower is better)*
- **BLER (Block Error Rate)** — fraction of frames with ≥1 error *(lower is better)*
- **Throughput** — useful information rate = `code_rate × (1 − BLER)` *(higher is better)*

---

## 📁 Project Structure

```
adaptive_coding_project/
├── adaptive_coding_project/       # Source package
│   ├── main.py                    # Simulation engine, adaptive logic, plotting
│   ├── channel.py                 # RayleighChannel — fading + AWGN
│   ├── modulation.py              # BPSKModulator — modulate/demodulate + LLRs
│   ├── ldpc_coding.py             # LDPC construction, encode/decode
│   ├── polar_coding.py            # Polar code construction, encode/decode
│   └── utils.py                   # BER calc, dB↔linear helpers
├── results/                       # Generated plots (created on run)
├── requirements.txt               # Python dependencies
├── project_explanation.md         # Beginner-friendly walkthrough
├── .gitignore
└── README.md
```

---

## ⚙️ How It Works

**1. Channel — `RayleighChannel`**
Applies `y = h·x + n`, where `h` is complex Rayleigh fading (`E[|h|²]=1`) and `n` is AWGN scaled to the target SNR (Eb/N0).

**2. Modulation — `BPSKModulator`**
Maps bits to ±1, then on receive performs Zero-Forcing equalization (`r = y/h`) and computes soft LLRs `2·Re(y·h*)/σ²`, correctly weighting by channel reliability `|h|²` — the matched-filter optimum.

**3. Coding — LDPC & Polar**
LDPC uses a fixed block length `n = 600`; Polar uses `n = 512` (power of two). Both are generated once per rate for efficiency.

**4. Adaptive logic**
Based on effective SNR, the controller targets a rate — `1/3` (noisy) → `1/2` (moderate) → `3/4` (clean) — and, among candidates, picks the scheme with the lowest BLER, capturing the classic ACM throughput staircase.

---

## 🚀 Quick Start

Works on any machine with **Python 3.8+**.

```bash
# 1. Clone
git clone https://github.com/miteshreddy08/adaptive_coding_project.git
cd adaptive_coding_project

# 2. Create & activate a virtual environment
python -m venv venv
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run the simulation (generates all plots in results/)
python adaptive_coding_project/main.py
```

You'll see a live SNR-by-SNR table in the terminal, and the `results/` folder will fill with plots when it finishes.

---

## 🛠️ Requirements

- **Python 3.8+**
- `numpy` · `matplotlib` · `pyldpc` · `numba` · `scipy`

*(all pinned in `requirements.txt`)*

---

## 🎓 New to This Topic?

See **[`project_explanation.md`](project_explanation.md)** for a friendly, jargon-free walkthrough that explains SNR, fading, and adaptive coding using a simple walkie-talkie analogy.

---

## 📜 License

Developed for research and educational purposes in Adaptive Coding and Modulation schemes.
