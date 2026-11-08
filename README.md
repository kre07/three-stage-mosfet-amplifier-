# Three-Stage MOSFET Voltage Amplifier | KiCad & SPICE

**An open-loop, transistor-level voltage amplifier designed using two common-source gain stages and a source-follower output buffer.**

![Three-stage MOSFET amplifier schematic](images/circuit-schematic.png)

## Project overview

The objective was to design and simulate a MOSFET amplifier powered from a **3.3 V supply**, achieving high voltage gain and wide bandwidth while driving a **10 kΩ / 2 pF load**. The circuit uses discrete transistor-level stages, with device sizing and bias selection supported by hand calculations and SPICE simulations.

### Architecture

| Stage | Configuration | Function | Reported simulated gain |
| --- | --- | --- | ---: |
| 1 | Common-source | First high-gain voltage amplification stage | ~37.1 dB |
| 2 | Common-source | Additional voltage gain | ~38.2 dB incremental |
| 3 | Source follower (common-drain) | Output buffering and load drive | ~−2.3 dB incremental |

**Signal path:** `Input → Common Source (CS1) → Common Source (CS2) → Source Follower → 10 kΩ / 2 pF load`

The two common-source stages provide gain, while the source follower helps drive the output load at reduced output impedance. The source follower's loaded voltage gain is less than unity.

## Results and design targets

*The values below are transcribed from the design and simulation report; they have not been independently re-simulated for this repository.*

| Metric | Target | Reported result | Assessment |
| --- | ---: | ---: | --- |
| Overall voltage gain | ≥ 60 dB | ~73–74 dB | Meets |
| Bandwidth | ≥ 500 kHz | ~3.91 MHz | Meets |
| Peak-to-peak output swing | ≥ 1.5 Vpp | ~2.277 Vpp | Meets |
| Input resistance | ≥ 100 kΩ | ~730 kΩ | Meets |
| Maximum branch current | ≤ 200 µA | ~137 µA | Meets |
| Loaded gain reduction | ≤ 10% | ~23.3% (report's value) | **Exceeds limit** |
| Power consumption | ≤ 1.0 mW | ~1.01 mW (calculated) | **Slightly exceeds limit** |

The design report gives a manual gain estimate of **78.8 dB** and an AC simulation result of approximately **73 dB** (rounded to 74 dB in its performance table). These are estimates from different methods, not separate tested versions of the circuit.

> **Measurement note:** The report calls the gain change from the second-stage output to the source-follower output a 2.3 dB reduction and labels it ~23.3%. The comparison is based on stage-to-stage gain, rather than a dedicated loaded-versus-unloaded test. The 10% load-sensitivity requirement therefore needs a separate verified measurement before claiming compliance.

## AC frequency response

![AC frequency-response plot](images/ac-frequency-response.png)

The AC analysis compares the first-stage, second-stage, and final-output gain responses. The report identifies approximately **3.91 MHz bandwidth** and a final midband voltage gain of approximately **73 dB**.

## Transient response and output swing

![Transient output-swing simulation](images/transient-output-swing.png)

The reported transient output voltage varies from roughly **0.243 V to 2.52 V**, giving:

`Vpp = 2.52 V − 0.243 V = 2.277 V`

The report notes some large-signal compression near the output peaks.

## Design details

- **Technology:** NMOS and PMOS devices using provided 3.3 V SPICE transistor models
- **Supply:** 3.3 V
- **Gain stages:** two common-source stages
- **Output stage:** one source follower
- **Load:** 10 kΩ resistor in conjunction with 2 pF capacitance
- **Biasing:** resistive bias network and fixed DC bias sources
- **EDA tools:** KiCad schematic editor and SPICE simulation

### Transistor sizing (from report)

| Devices | Type | Width | Length | W/L |
| --- | --- | ---: | ---: | ---: |
| Q1, Q4 | NMOS | 12 µm | 0.5 µm | 24 |
| Q2, Q3 | PMOS | 48 µm | 0.5 µm | 96 |
| Q5 | NMOS | 80 µm | 0.5 µm | 160 |
| Q6 | NMOS | 19 µm | 0.5 µm | 38 |

## Repository layout

```text
three-stage-mosfet-amplifier/
├── README.md
├── .gitignore
├── hardware/
│   ├── three-stage-mosfet-amplifier.kicad_pro
│   └── three-stage-mosfet-amplifier.kicad_sch
├── models/
│   ├── nmos_t.txt
│   └── pmos_t.txt
├── images/
│   ├── circuit-schematic.png
│   ├── ac-frequency-response.png
│   └── transient-output-swing.png
└── docs/
    └── design-report.pdf
```

## Opening the design and simulating

1. Install a compatible version of **KiCad** (the source project was saved using KiCad 10.0).
2. Open `hardware/three-stage-mosfet-amplifier.kicad_pro`.
3. Check each transistor's SPICE model mapping in the schematic. The model paths are relative to the `hardware/` directory: `../models/nmos_t.txt` and `../models/pmos_t.txt`.
4. In the KiCad simulator, use DC operating-point, AC sweep, and transient analyses to investigate circuit behaviour. The diagrams and values shown above are from the documented runs; simulation reproduction on another installation has not been verified.

**Model licensing:** These supplied transistor model parameter files may originate from a third-party process library. They are included for project completeness, but **confirm you have redistribution permission before making this repository public**. If permission cannot be established, remove `models/` from the public version and obtain the required models through the authorized provider.

**Scope:** Schematic-level design and simulation, not a fabricated chip or PCB. Simulation result files and an automated testbench are not part of this repository.

## Engineering observations

- Cascading two common-source amplifiers provides strong voltage gain, but a source-follower output buffer introduces some loss under load.
- The design exceeded gain, bandwidth, swing, input-resistance, and branch-current targets, while showing tradeoffs in loaded gain reduction and power.
- Hand calculations provide a useful first approximation, whereas device-level SPICE modeling captures additional loading and non-ideal behavior.


## Author

**Kritagya Sharma**
