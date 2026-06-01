# Low-Power Single-Ended Conditional Capture Flip-Flop in 22nm CMOS

> **Course Project — Integrated Circuits Design**  
> Department of Electrical and Computer Engineering, Birzeit University

---

## Overview

This project presents the design and physical implementation of a **low-power single-ended Conditional Capture Flip-Flop (CCFF)** in 22nm CMOS technology. The design targets significant clock power reduction while maintaining competitive timing performance, achieving **90–99% power reduction** compared to reference designs in 180nm and 250nm technologies.

The core idea: conventional flip-flops waste energy by switching internal nodes every clock cycle, even when the input data hasn't changed (D = Q). The CCFF solves this by using a NOR-based comparison mechanism that **suppresses internal switching when D = Q**, reducing dynamic power proportional to data correlation.

---

## Key Results

| Metric | Pre-Layout | Post-Layout |
|---|---|---|
| Technology | 22nm CMOS | 22nm CMOS |
| Supply Voltage | 0.95 V | 0.95 V |
| Transistor Count | 26 | 26 |
| Silicon Area | — | **8.8 μm²** |
| Clock-to-Q Delay | 47.30 ps | 52.03 ps |
| Total Dynamic Power | 1.96 μW | 2.33 μW |
| Clock Power | 0.154 μW | 0.166 μW |
| Clock Power % | 7.9% | — |
| Setup Time | −200 ps | −180 ps |
| Hold Time | 230 ps | 260 ps |
| Power-Delay Product (PDP) | 0.093 fJ | 0.121 fJ |

### Power Improvement vs. Reference Designs

| Reference | Technology | Their Power | Our Reduction | Improvement |
|---|---|---|---|---|
| CDFF (Paper 1) | 180nm | 20.2 μW | 90.3% | 10.3× |
| imCCFF (Paper 1) | 180nm | 27.0 μW | 92.7% | 13.8× |
| CCFF Single (Paper 2) | 250nm | 272 μW | 99.3% | 138.9× |
| SAFF Diff (Paper 2) | 250nm | 467 μW | 99.6% | **238.5×** |

---

## Architecture

The proposed CCFF consists of four functional blocks:

1. **NOR-based State Comparator** — Compares input D with current output Q
2. **PMOS Precharge Transistor (M1)** — Pulls internal node X to VDD when CLK = 0
3. **Conditional Discharge Path** — Activates only when D ≠ Q, gating unnecessary transitions
4. **Output Buffer/Latch** — Drives the final Q and QB outputs

### Operating Principle

```
CLK = 0  →  Precharge phase: X_bar pulled to VDD
CLK = 1  →  Evaluation phase:
              if D ≠ Q  →  discharge X_bar  →  output Q updates
              if D = Q  →  X_bar holds high  →  no internal switching (power saved)
```

The negative setup time (−200 ps) is a key advantage of this architecture, enabling **time borrowing** across pipeline stages.

---

## Design Flow

```
Transistor Sizing (LTspice)
        ↓
Schematic Design (Electric VLSI)
        ↓
Pre-Layout Simulation (SPICE / 22nm PDK)
        ↓
Physical Layout (Electric VLSI — DRC clean)
        ↓
Parasitic Extraction (RC netlist)
        ↓
Post-Layout Simulation (SPICE with parasitics)
        ↓
Performance Comparison vs. Reference Designs
```

---

## Tools & Technology

| Tool / Technology | Purpose |
|---|---|
| **Electric VLSI** | Schematic capture and physical layout |
| **LTspice / SPICE** | Pre-layout and post-layout simulation |
| **22nm Bulk CMOS PDK** | Process design kit (TT corner, 25°C) |
| **IEEE TCAS II Template** | Report format |

---

## Repository Structure

```
📁 CCFF-22nm-CMOS/
├── 📄 README.md
├── 📁 design/
│   ├── ccff.jelib              # Electric VLSI library (schematic + layout)
├── 📁 simulation/
│   ├── singleEndedFlipFlopSch.raw     
│   ├── singleEndedFlipFlopSch.spi    # Pre-layout and Post-layout SPICE netlist (with parasitics)
├── 📁 docs/
│   ├── ENCS333_IC_Project.pdf  # Project documentation
│   ├── Low-Power Single-Ended Conditional Capture Flip.pdf   # Full paper (IEEE TCAS II format)
│   └── Presentation - Integrated Circuits Project.pdf        # Project presentation slides
└── 📁 results/
    └── waveforms/              # Simulation screenshots
```

---

## Simulation Setup

- **Process Corner:** Typical–Typical (TT)
- **Temperature:** 25°C
- **Supply Voltage (VDD):** 0.95 V
- **Load Capacitance (CL):** 5 fF
- **Operating Frequency:** 500 MHz (swept from 100 MHz to 2 GHz)
- **Clock Transition Time:** 10% of period

To run pre-layout simulation:
```spice
.include "22nm_LP.txt"
* Open ccff_prelayout.spi in LTspice and run .tran simulation
```

---

## References

1. N. Nedovic, M. Aleksic, and V. G. Oklobdzija, "Conditional-capture flip-flop for statistical power reduction," in *Proc. IEEE Int. Soc. ASIC/SOC Conf.*, 2001, pp. 137–141.

2. P. Zhao, J. McNeely, W. Kuang, N. Wang, and Z. Wang, "Design of sequential elements with reduced clocking power," *IEEE Trans. Very Large Scale Integr. (VLSI) Syst.*, vol. 12, no. 5, pp. 424–435, May 2004.
