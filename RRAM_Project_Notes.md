# RRAM Final Project - Working Notes

Project: research paper + presentation on RRAM devices for MOS Device Modeling
Owner: Ashton (ashtonrb1@gmail.com)
Started: 2026-04-30
Last full rewrite: 2026-05-02

> **Note for any agent picking this up.** The framing of this project has
> evolved. The current spine is **the embedded-NVM scaling crisis** -
> embedded flash hits a wall around 28 nm and emerging memories
> (RRAM, MRAM) are the answer, with the per-node integration story
> following the host MOSFET architecture from planar bulk through
> FD-SOI to FinFET. Course relevance comes from the select-FET design
> problem in 1T1R cells: it is a real MOSFET sizing problem that
> changes character at every architectural step.
>
> The paper outline the user has settled on:
>
>   1. **What RRAM is.** Definition, MIM stack, Waser's three classic
>      categories (VCM / ECM / TCM), filamentary vs. bulk axis. Short
>      acknowledgement of alternatives (PCM, MRAM, FeRAM/FeFET) noting
>      they are out of scope.
>   2. **Traditional embedded memory and why scaling forced a
>      transition.** Embedded flash and its tunnel-oxide bottleneck.
>      Why the eNVM market needs a non-floating-gate replacement at
>      advanced nodes.
>   3. **Where RRAM has and has not taken off.** It is the standard
>      embedded-NVM solution at 28 nm and below; it has not displaced
>      DRAM or NAND in standalone main memory.
>   4. **The cutting edge.** What current research is trying to fix
>      so RRAM can extend further: variability, forming-free operation,
>      selectorless 1S1R, in-memory computing, integration at FinFET
>      and GAA nodes.
>
> The four-paper folder analysis from the original notes has been
> condensed into Section 5; the source-guide annotated bibliography
> in `RRAM_Source_Guide.docx` remains the primary reference index.

---

## 0. Files in the folder

- `s41467-024-46682-1.pdf` - **Park et al. 2024**, *Nat. Commun.* 15, 3492.
  Filament-free trilayer (Al2O3 / TiO2 / TiOx) bulk-switching RRAM,
  Kuzum group, UCSD.
- `Torrezan_2011_Nanotechnology_22_485203.pdf` - **Torrezan et al. 2011**,
  *Nanotechnology* 22, 485203. Sub-nanosecond switching of a TaOx
  memristor, HP Labs.
- `nmat3070.pdf` - **Lee et al. 2011**, *Nat. Mater.* 10, 625.
  Asymmetric Ta2O5-x / TaO2-x bilayer with built-in Schottky selector,
  Samsung SAIT.
- `srep28155.pdf` - **Niu et al. 2016**, *Sci. Rep.* 6, 28155. HfO2
  1T1R RRAM in 0.25 um CMOS by batch ALD, IHP Microelectronics.
- `RRAM_Source_Guide.docx` - 21-source annotated bibliography prepared
  earlier. Read first if you need to find a specific reference.
- `RRAM_Comparison_Matrix.xlsx` - 3-sheet workbook: tech-comparison
  matrix, source list, value-judgement scoreboard.

---

## 1. What is RRAM

### Definition

Resistive Random Access Memory (RRAM, or ReRAM) is a class of
non-volatile memory in which information is stored as the resistance
of a passive two-terminal cell, typically a metal-insulator-metal
(MIM) stack. Voltage pulses switch the cell between a high-resistance
state (HRS, the "0") and a low-resistance state (LRS, the "1"), and
those transitions are called RESET and SET respectively. Most RRAM
cells require a one-time **forming** step that creates the active
switching region; once formed the cell cycles between HRS and LRS.

The MIM cell is small (~20-50 nm tall) and is built **in the
back-end-of-line (BEOL)** of a CMOS wafer between two metal
interconnect layers. This is what makes RRAM a "BEOL-integrable"
memory and is the central reason it is attractive as embedded NVM:
storage sits above the transistors rather than competing with them.

Source for the field-defining "memristor / resistive switching is
real" claim: Strukov, Snider, Stewart & Williams, *Nature* 453, 80
(2008). DOI 10.1038/nature06932. Source guide entry 1.

### Waser's three classic categories

The standard taxonomy comes from Waser, Dittmann, Staikov & Szot,
"Redox-based resistive switching memories: nanoionic mechanisms,
prospects, and challenges," *Adv. Mater.* 21, 2632 (2009). DOI
10.1002/adma.200900375. Source guide entry 2. The three buckets
are:

**1. Valence Change Mechanism (VCM).** The mobile species is the
oxygen anion (or, equivalently, the oxygen vacancy V_O). Under field,
oxygen drifts out of a region of the oxide, leaving an
oxygen-deficient, sub-stoichiometric, more conductive sub-oxide
filament. The metal cation in the filament is reduced to a lower
oxidation state (Hf4+ -> Hf3+, Ta5+ -> Ta4+). Reverse the bias and
oxygen returns. Bipolar. This is the family that includes
**HfO2 RRAM** (Niu 2016, srep28155 in folder) and **TaOx RRAM** (Lee
2011, nmat3070 in folder; Torrezan 2011, Nanotechnology 22, 485203 in
folder), and is the dominant family commercially.

**2. Electrochemical Metallization (ECM, also called CBRAM).** The
mobile species is a metal cation - Ag+ or Cu2+ - from an
electrochemically active electrode. Under positive bias, the metal
dissolves into a solid electrolyte (GeSe, GeS2, SiOx, doped HfO2),
drifts to the inert counter-electrode, and electrocrystallizes into a
metallic filament that bridges the gap. Reverse bias dissolves the
filament. Bipolar. Voltages are typically very low (sub-0.5 V),
because the limit is redox kinetics, not bond breaking. CBRAM is
**not represented** in the four PDFs in the folder. Primary review:
Zahoor, Zulkifli & Khanday, *Micromachines* 13, 725 (2022). Source
guide entry 12.

**3. Thermochemical Mechanism (TCM).** Switching is driven by Joule
heating, not by a clean redox pair. Unipolar - SET and RESET happen at
the same polarity but different current levels. SET creates a
filament via local thermochemical reduction; RESET ruptures the
filament by dumping a high current that locally heats it past its
oxidation or melting point. NiO is the canonical TCM material. TCM
has lost commercial relevance because the variability is intractable -
each RESET is essentially a controlled local meltdown - and the
RESET energy is high. Source for TCM as a category and NiO behavior:
Waser et al. 2009 (taxonomy); also Sawa, *Mater. Today* 11, 28 (2008)
referenced by Niu 2016 (ref. 28). Niu 2016 itself flags the
filamentary-vs-interface distinction at p4: "two main categories of
physical models of RS can be classified, i.e. interface type ... and
filament type."

### A second axis: filamentary vs. bulk (area-type)

Waser's three buckets are about *what is moving*; an orthogonal axis
is about *where it moves*. Most VCM and all ECM is **filamentary**:
one or a few conductive paths form, and HRS/LRS resistance is
independent of cell area. Some VCM systems are **bulk** (also called
area-type or interface-type): oxygen vacancies redistribute uniformly
across the layer, and resistance scales with area.

In the folder, Niu 2016 demonstrates the filamentary type explicitly
(Fig. 4: HRS and LRS independent of cell area from 1 to 0.36 um^2,
"the filament mechanism dominants the RS behaviour", p4-5). Park 2024
demonstrates the bulk type explicitly (Fig. 3b: slope-1 area scaling,
"resistance linearly scales with the area for both HRS and LRS,
suggesting bulk switching", p3). The two together are the cleanest
filamentary-vs-bulk contrast available without leaving the folder.

### Brief notes on alternatives (not covered in scope)

The paper will name these once for landscape context and not pursue
them. Each is a distinct emerging-NVM technology with its own
literature.

- **Phase Change Memory (PCM).** Chalcogenide (Ge2Sb2Te5 family) that
  switches between amorphous (HRS) and crystalline (LRS) phases under
  Joule heating. Shipped commercially as Intel/Micron 3D XPoint /
  Optane until discontinuation. *Not in Waser's RRAM taxonomy.*
  Useful comparator for "another emerging NVM that did ship."
- **Magnetic RAM (MRAM, STT-MRAM, SOT-MRAM).** Magnetic tunnel
  junction with two ferromagnetic layers; resistance depends on
  parallel/antiparallel magnetization. Shipped as embedded MRAM at
  Samsung 28 nm FD-SOI and 14 nm FinFET. RRAM's main competitor at
  advanced eNVM nodes. Source for the "RRAM and MRAM are the
  frontrunners" framing: Hellenbrand, Teck & MacManus-Driscoll, "Progress
  of emerging non-volatile memory technologies in industry," *MRS
  Communications* 14, 1099 (2024). DOI 10.1557/s43579-024-00660-2.
  Source guide entry 18. Open access via PMC11618178.
- **Ferroelectric memories (FeRAM, FeFET).** Use a ferroelectric
  capacitor or gate dielectric whose polarization state encodes the
  bit. Especially relevant because modern FeFETs use ferroelectric
  HfO2 - the *same* dielectric as HfO2 RRAM and HKMG MOSFETs. A
  genuinely interesting MOS-modeling tie-in that the paper acknowledges
  but does not pursue.
- **2D-material memristors.** h-BN-based RRAM (Lanza group, KAUST)
  integrated by transfer onto finished CMOS. Source guide entries 15
  and 16. Pre-commercial.

---

## 2. Traditional embedded memory and why scaling forced a transition

### What embedded NVM is and why it matters

Embedded non-volatile memory (eNVM) is on-die NVM that lives next to
logic in an SoC. It stores firmware, calibration constants, AI model
weights, secure keys, and small data caches that have to survive
power-off. Microcontrollers, automotive ICs, IoT SoCs, and most
consumer SoCs all need eNVM. From the late 1990s through about 2017,
embedded flash (eFlash) was the dominant eNVM technology. The
question is why it stopped being viable.

### The eFlash tunnel-oxide bottleneck

Embedded flash uses a floating-gate (or charge-trap SONOS variant)
MOSFET. Charge stored on the floating gate shifts the threshold
voltage. Retention - the requirement that the cell still reads correctly
after 10 years at 85 C - sets a minimum tunnel oxide thickness through
direct and Fowler-Nordheim tunneling, of roughly 7-10 nm. **This
thickness does not scale with the logic node.** You can shrink the
floating-gate cell in plan view, but the oxide stack height stays the
same, so the cell aspect ratio worsens at every node.

Standard reference for the "memory leads computing because flash
scaling has limits" framing: **Wong & Salahuddin, "Memory leads the
way to better computing," *Nature Nanotechnology* 10, 191 (2015).
DOI 10.1038/nnano.2015.29.** Open access. This is the canonical
single-paragraph-summary citation for "flash hit a wall."

A more detailed treatment of why eFlash struggles at advanced nodes
and how RRAM and MRAM step in: **Ielmini & Wong, "In-memory computing
with resistive switching devices," *Nature Electronics* 1, 333 (2018).
DOI 10.1038/s41928-018-0092-2.** Source guide entry 5.

### The mask-count and process-complexity argument

eFlash adds photomasks and process steps to a logic flow. By 28 nm,
the eFlash module typically adds ~10-15 mask layers to a logic
process and a separate set of high-voltage charge-pump devices for
program/erase. At 16/14 nm FinFET, integrating a planar floating-gate
cell into a fin-based front-end is essentially incompatible with the
process flow without a major redesign. The result was that **TSMC
publicly stopped offering eFlash past 28 nm circa 2017-2018**, and
Samsung followed shortly after. Below 28 nm, eNVM has to be a
non-floating-gate technology - which is the door RRAM and MRAM walked
through.

> Source needed: a primary citation for the "TSMC eFlash stops at
> 28 nm" announcement. The original is in TSMC Technology Symposium
> proceedings 2018-2019 and TSMC investor materials. Industry press
> (EE Times, SemiWiki, AnandTech) covered it. **Need to chase down a
> primary source before quoting in the final paper.** The Hellenbrand
> 2024 MRS Communications review (cited above) summarizes the
> situation in its industry overview and is a defensible secondary
> source.

A 2024 industry snapshot that documents which foundries are shipping
which eNVM at which node, useful as a citable roadmap reference:
**TechInsights, "Embedded & Emerging Memory Technology Roadmap - May
2024."** Source guide entry 19. Trade-press, not peer-reviewed, but
valuable for the commercial-status column.

### Why this matters for the paper's framing

This is the commercial premise that justifies the entire embedded
RRAM push. If eFlash had scaled cleanly past 16 nm, RRAM would
probably still be a research curiosity. It is precisely *because*
eFlash hit a hard physical wall at advanced nodes that RRAM has a
real commercial niche. The paper should make this case explicitly
in the introduction.

---

## 3. Where RRAM has and has not taken off

> **Calibration note (added 2026-05-02 after user pushback).** An
> earlier version of this section overstated the case as "RRAM has
> taken over eNVM at advanced nodes." The honest picture is that
> RRAM is *one of three* viable answers to the post-28 nm eNVM gap,
> alongside embedded MRAM and "go off-chip with external SPI flash."
> MRAM is arguably ahead of RRAM by volume today. Section 3.5 covers
> the alternatives directly.

### Where it has gained meaningful traction: embedded NVM at advanced logic nodes

Between roughly 2018 and 2024, several foundries have introduced
embedded RRAM as a foundry-process option, and some are shipping in
commercial products:

- **TSMC**: HfO2-based eRRAM at 40 nm (planar bulk HKMG), with
  follow-on 28 nm and 22 nm offerings, and a 12FFC FinFET demonstration
  at IEDM 2023.
  - 40 nm macro: Chou et al., "An N40 256K x 44 embedded RRAM macro
    with SL-precharge SA and low-voltage current limiter," *ISSCC
    Digest* 478 (2018). DOI 10.1109/ISSCC.2018.8310385. Source guide
    entry 7.
  - 12FFC FinFET demo: Chih et al., "Emerging Memory RRAM Embedded in
    12FFC FinFET," *IEDM Tech. Dig.* session 21.3 (2023). Public
    handout: https://mys.mapyourshow.com/mys_shared/iedm23/handouts/21-3_Tue_15331.pdf.
    Source guide entry 8. **Not yet read directly - flag before quoting.**
- **GlobalFoundries + Weebit Nano**: TaOx ReRAM IP integrated into
  GF's 22FDX FD-SOI process. Public materials at
  https://www.weebit-nano.com/technology/embedded-reram-ip/ . Source
  guide entry 11. **Not yet read directly.**
- **SkyWater + Weebit Nano**: TaOx ReRAM IP in SkyWater's 130 nm
  planar bulk CMOS. Reportedly automotive Grade-1 qualified at
  125 C. Same Weebit Nano page. Source guide entry 11.
- **Adesto / Dialog / Renesas**: CBRAM in 130 nm and sub-90 nm logic.
  Historical industry-mapping reference: Kund et al., "Conductive
  bridging RAM (CBRAM): an emerging non-volatile memory technology
  scalable to sub 20 nm," *IEDM Tech. Dig.* 773 (2005). DOI
  10.1109/IEDM.2005.1609463. Source guide entry 13.

The 2024 industry snapshot supporting the "RRAM and MRAM are the two
emerging-NVM frontrunners in industry" claim: **Hellenbrand, Teck &
MacManus-Driscoll, *MRS Communications* 14, 1099 (2024).** Source
guide entry 18.

### The transistor-architecture story

The eRRAM cell is roughly the same across foundries; what changes is
the host MOSFET architecture. The integration narrative arc:

  0.25 um planar bulk (Niu 2016, srep28155 in folder, p1, p9)
    -> 40 / 28 / 22 nm planar bulk HKMG (TSMC; Chou 2018 ISSCC)
    -> 22FDX FD-SOI (GlobalFoundries + Weebit)
    -> 12FFC FinFET (TSMC IEDM 2023)

This is the spine of the integration section: the same RRAM cell
hosted by four different MOSFET architectures, each presenting a
different select-FET design problem. Section 6 below details those
problems.

### Section 3.5 - The competitive alternatives at advanced nodes

The question "if eFlash isn't offered, what is?" doesn't have RRAM as
a unique answer. Below 28 nm, an advanced-node SoC designer chooses
among:

**Embedded MRAM (STT-MRAM).** RRAM's main competitor and arguably the
larger of the two by current production volume. Magnetic tunnel
junction memory; resistance depends on parallel/antiparallel
magnetization of two ferromagnetic layers.

- **Samsung Foundry**: shipping eMRAM at **28FDSOI** since ~2019,
  used in IoT and automotive products. eMRAM also offered at 14LPP
  and 8LPP FinFET.
- **TSMC**: eMRAM at 40 nm, 22 nm, and 16/12 nm FinFET classes.
- **GlobalFoundries**: eMRAM at 22FDX (alongside Weebit ReRAM IP, so
  22FDX customers actually choose between two emerging-NVM
  technologies on the same process).
- **Intel**: research/development in 22FFL.

MRAM advantages over RRAM: orders-of-magnitude better endurance
(~10^14 cycles for STT-MRAM vs 10^5-10^7 for production HfO2 RRAM),
deterministic switching (no filament stochastics), more mature
reliability data. MRAM disadvantages: larger and more complex bit
cell, higher mask adder, lower density, often higher write energy.
**Tooling note:** specific node-by-node availability statements
(e.g., "Samsung 28FDSOI eMRAM since 2019") are propagated from
industry-press knowledge and should be verified against primary
foundry materials before quoting in the final paper.

**Use-case split between RRAM and MRAM at advanced nodes:**
- MRAM tends to win when endurance matters (cache-like uses,
  frequent rewrites, working memory).
- RRAM tends to win when cost / density matter and writes are
  infrequent (firmware code, AI weights, calibration data).

The two-frontrunner framing is supported by **Hellenbrand et al.,
*MRS Communications* 14, 1099 (2024)** (source guide entry 18):
"RRAM and MRAM are the two frontrunners."

**One-time programmable (OTP) and antifuse.** Tiny amounts of NVM -
kilobytes, not megabytes - built from one-shot dielectric breakdown
of the gate oxide. Synopsys (formerly Sidense) and Kilopass IP are
the standard licensable cores. Available at every node from 180 nm
down through 5 nm because the underlying mechanism is just gate-oxide
breakdown, which the foundry already characterizes for reliability.
Uses: calibration constants, security keys, chip IDs, eFuses for
redundancy mapping. **Limitation:** write-once, no rewrite.

**External SPI/QSPI NOR flash, on-die SRAM cache.** The dominant
strategy for high-end SoCs at FinFET and GAA. Apple A/M, leading
mobile SoCs, GPUs - most have no embedded NVM at all. Firmware lives
on an external NOR or NAND chip on a serial bus (SPI / QSPI /
OctaSPI / HyperBus). On-die SRAM is the working cache. Trades off
integration density and BOM cost for process simplicity. At leading
edge nobody cares because they're already shipping multi-die
packages with HBM or external DRAM.

**Embedded ferroelectric memory (FeFET / FeRAM).** Older 1T1C FeRAM
shipped at TI and Cypress / Infineon at 130/90 nm. The newer
ferroelectric HfO2 (HZO) flavor - same dielectric as HfO2 RRAM and
HKMG - is in research / limited commercial development at GF
28FDSOI and Infineon. Production-grade FeFET at advanced nodes is
mostly pre-commercial. **Worth one paragraph in the paper** as a
"same HfO2 wearing a different hat" course tie-in.

**Charge-trap eFlash extensions.** SST SuperFlash and similar push
floating-gate / charge-trap eFlash deeper than 28 nm via clever
integration. Niche IP, not foundry mainline at FinFET.

**By-volume note.** *Across the whole eNVM market*, embedded flash
still dominates by total wafer volume, because most MCU and IoT
shipments are at 90/65/40 nm where eFlash works fine. The shift
described in Section 2 is happening only at the leading edge. The
paper should not say "RRAM has taken over eNVM" without that node-
qualifier.

### Section 3.6 - RRAM vs MRAM head-to-head

If the paper takes the "two-frontrunner" framing seriously, it needs a
side-by-side comparison of the two technologies on the metrics that
actually drive technology choice. This section condenses what the
literature and industry sources say.

**Where RRAM wins:**

- **Cell simplicity.** MIM stack, two terminals, three or four thin
  films. Mask adder ~1-2 (HfO2 RRAM). MRAM mask adder is more like
  ~5-7 because the magnetic tunnel junction has multiple ferromagnetic
  and oxide layers with sub-nm thickness control.
- **Material compatibility.** HfO2, TaOx, TiN, Ti are already in the
  fab (HKMG, Cu liners). MRAM needs CoFeB, MgO, ruthenium, and
  dedicated deposition tools that are not part of standard CMOS.
- **Cell area / density.** Smaller bit cell at a given node, higher
  density per bit, lower cost per bit at scale.
- **Multi-level capability.** Conductance is continuous, so 4, 8, 16,
  or even 100 distinct conductance levels are possible per cell
  (Park 2024 demonstrates 100, p4-5). MRAM is fundamentally binary
  because magnetization is binary by physics, so MLC is much harder.
  This makes RRAM the obvious choice for analog in-memory computing
  and neural-network weight storage.
- **3D / crossbar potential.** Selectorless 1S1R crossbars stacked
  vertically give RRAM a path to extremely high density. MRAM does
  not have an equivalent path.
- **Switching energy.** Picojoule-class at the cell level (Torrezan
  2011 reports 1.9 pJ SET / 5.8 pJ RESET for a 2 um device, p3),
  often sub-pJ in modern compact-area cells.

**Where MRAM wins:**

- **Endurance.** STT-MRAM hits ~10^14 cycles routinely; production
  HfO2 RRAM is ~10^5-10^7. That is seven to nine orders of
  magnitude. For any write-heavy workload (cache, working memory,
  frequently-updated state), MRAM is the only credible choice. RRAM
  is restricted to write-once / write-rare workloads (firmware,
  AI weights, calibration constants).
- **Deterministic switching.** Magnetic state is binary by physics,
  so there is no equivalent of filament stochastics. Cycle-to-cycle
  and device-to-device variation is much tighter. Tail bits, the
  bane of RRAM yield, are far less of a problem in MRAM.
- **No forming step.** RRAM cells need a one-time forming voltage
  higher than normal SET (Niu 2016 reports forming ~3.2-4.0 V vs.
  SET ~0.8-1.1 V, Fig. 2). That requires a charge pump and a
  wafer-test step. MRAM cells work on first power-up.
- **Reliability data is more mature.** MRAM has been in development
  since the 1990s and has been qualified for automotive Grade-1 at
  multiple foundries. RRAM is catching up but the qualification base
  is shallower.
- **Read margin distributions.** RRAM has much larger HRS/LRS
  ratios per cell (10x to 1000x for HfO2 / TaOx; Lee 2011 Fig. 1d)
  vs ~2-3x for MRAM (TMR ~150-200%), but RRAM's broader cell-to-cell
  distributions can eat into that margin. After tails are accounted
  for, the *effective* read margins are comparable, with MRAM often
  ahead at large array sizes.

**Where MRAM loses to RRAM, beyond the items above:**

- **Larger bit cell** because of the MTJ stack and the wider select
  transistor needed to source the higher write current.
- **Higher write current** (50-200 uA per bit for STT-MRAM vs sub-50
  uA for HfO2 RRAM at small cells). This drives the select-FET
  sizing problem in the wrong direction.
- **Higher cost per bit** at volume.
- **Field sensitivity** in some configurations - shielding may be
  required for security/military applications.

**Use-case split that the paper should adopt:**

- **MRAM** when endurance matters (cache-like uses, frequently-
  rewritten data, anything aiming at SRAM/DRAM-replacement roles).
- **RRAM** when cost / density matter and writes are infrequent
  (firmware code, AI inference weights, calibration data, secure
  keys, neural-network MAC arrays).

The two technologies are not directly displacing each other. They
serve overlapping but distinguishable embedded-NVM workloads, and
that is why both exist at advanced nodes.

> **Verification flag.** Specific cycle/endurance numbers for STT-
> MRAM at production foundries (Samsung 28FDSOI, TSMC 22nm, GF 22FDX)
> and the per-foundry reliability qualification claims should be
> cross-checked against IEDM/ISSCC papers from the relevant foundries
> (2018-2024) before going into the final paper. The 10^14 endurance
> figure is the textbook STT-MRAM number but exact production
> guarantees vary by foundry.

### Section 3.7 - Why embedded NVM at all? (vs external SPI flash)

This question is what determines whether eRRAM and eMRAM matter at
all, or are research curiosities. The honest answer: external SPI
flash is the right choice for high-end SoCs, and embedded NVM is the
right choice for low-power, secure, real-time, or cost-constrained
products. The two markets are mostly distinct.

**Where embedded NVM wins decisively:**

- **Latency and bandwidth.** Embedded NVM lives on the on-die bus -
  code fetches happen in nanoseconds at full bus width (32 or 64
  bits per cycle at chip clock). External SPI flash is serial: even
  Quad-SPI is 4 bits per clock, OctaSPI is 8, with overhead per
  transaction. Code execute-in-place (XIP) from external flash is
  fundamentally bandwidth-limited; cache miss penalty is hundreds of
  nanoseconds. For real-time MCUs running ISRs or motor control
  loops, embedded NVM is the only option that meets timing.
- **Power and quiescent current.** External flash adds dynamic power
  for the SPI interface, the external chip itself, and PCB-level
  signaling. Battery-powered IoT and BLE devices that wake briefly
  from deep sleep cannot tolerate this. Embedded NVM access is
  on-die-only - orders of magnitude lower energy per fetch. This is
  the single dominant reason a BLE SoC has embedded flash and not
  external.
- **Boot time.** Embedded NVM is instant on power-up. External flash
  requires ROM boot, SPI configuration, and either XIP startup or
  copy-to-SRAM. Wake-from-deep-sleep latency goes from microseconds
  to milliseconds.
- **Security.** Embedded NVM is buried inside the same die as the
  CPU - much harder to physically tamper with. External flash is a
  separate chip whose bus can be snooped, whose chip can be
  replaced, and whose contents can be read off with off-the-shelf
  programmers. Secure-boot keys, DRM keys, biometric templates, and
  authentication credentials must live in embedded NVM (or eFuse /
  OTP) to meet modern security requirements. This drives the
  embedded-NVM requirement in automotive MCUs, smart card SoCs,
  secure elements, and payment terminals.
- **System BOM cost at small system sizes.** Embedded = one chip.
  External = SoC + flash chip + PCB area + SPI routing. Below
  approximately 16-32 MB code/data footprint, embedded is cheaper
  per system; above that, external NAND becomes cheaper per bit.
- **Form factor.** Wearables, hearing aids, implantable medical
  devices, smart cards - no physical room for two chips.
  Single-die-only is a hard requirement, and embedded NVM is the
  only way to ship them.
- **Reliability and harsh environments.** Fewer solder joints,
  fewer board-level signal integrity concerns, simpler PCB.
  Automotive Grade-1 (AEC-Q100) qualification is dramatically
  easier with embedded NVM.

**Where external SPI flash wins:**

- **Large footprints** (>32 MB code, >100 MB data). Embedded NVM
  becomes cost-prohibitive at large sizes; external NOR / NAND is
  much cheaper per bit and has decades of cost-reduction experience.
- **High-end SoCs** (mobile APs, GPUs, datacenter chips) where the
  package already includes external memory anyway. Adding embedded
  NVM gives nothing - the BOM is built around external chips.
- **Frequent over-the-air updates** where erase/program cycle count
  matters more than retention.
- **Cost-driven legacy designs** where external flash has been
  amortized over many product generations.

**The market crossover.**

- **Embedded NVM market** = MCUs, BLE chips, sensor nodes, smart
  cards, automotive ICs, secure elements, hearing aids, medical
  implants, low-end IoT. Real volume - billions of units per year.
  This is where eRRAM and eMRAM compete.
- **External-flash market** = mobile APs, GPUs, AI accelerators,
  server CPUs, large embedded systems. eRRAM and eMRAM are not
  competing here.

**Why this matters for the paper.** The eRRAM/eMRAM market is the
embedded-system end of the chip industry, not the leading-edge AP /
GPU market. When the paper says "RRAM is filling the eFlash gap at
advanced nodes," the relevant market is microcontrollers and IoT
SoCs built on advanced logic processes - which is a real,
high-volume market with strong reasons to care which emerging-NVM
technology wins (unit cost, security qualification, battery life).
RRAM and MRAM aren't fighting Apple's M-series for relevance;
they're fighting for the firmware/calibration/secure-key role in
every advanced-node MCU and IoT SoC.

That framing also explains why the *standalone* RRAM market never
took off (Crossbar Inc., Adesto's standalone CBRAM): the value
proposition of RRAM is integration-driven (latency, power,
security, BOM) and disappears when you put it in its own package.

### Where it has not taken off: standalone main memory

RRAM has *not* displaced DRAM or NAND flash in standalone (off-chip)
memory. Two reasons:

**Variability and reliability are not DRAM-grade.** Filamentary RRAM
(HfO2, TaOx) has cycle-to-cycle and device-to-device variation that
is much worse than DRAM. To use RRAM in a DRAM role you would need
endurance in the 10^9-10^12 range with tight distributions; production
HfO2 RRAM is closer to 10^5-10^7 with broader tails. Niu 2016 (Fig. 3,
p4) reports endurance of ~300 cycles for unoptimized HfO2 1T1R cells.
Lee 2011 (Fig. 2c) demonstrated >10^12 cycles in optimized lab TaOx
asymmetric bilayers, but that is a single-device research result, not
a production figure.

**Cost cannot compete with commodity DRAM and NAND.** Standalone DRAM
is built in dedicated DRAM fabs with vertical capacitors that have
benefitted from 30 years of process refinement. Standalone NAND is
3D-stacked to 200+ layers. Cost per bit on these technologies is far
below what RRAM can hit at the volumes RRAM has achieved. Crossbar
Inc. attempted to commercialize standalone OxRAM in the 2014-2020
window and did not reach mass-market scale. Adesto pivoted from
standalone CBRAM to embedded before being acquired by Renesas.

The honest summary that the paper should deliver: **RRAM has taken
the niche it was actually competitive in (advanced-node embedded
NVM), and has not crossed into the commodity main-memory market that
it was once positioned against.** That is a success story, not a
failure - it just isn't the success story the early hype implied.

---

## 4. The cutting edge: what current research is trying to fix

The technologies above ship today, but several characteristics of
filamentary RRAM keep it out of larger markets. Current research is
organized around fixing these. The folder contains one paper in this
category (Park 2024) and the source guide points to others.

### Variability and the case for bulk / non-filamentary switching

The dominant reliability weakness of filamentary HfO2 / TaOx is
that filament formation and rupture are stochastic events. Random
oxygen-vacancy positions translate into cycle-to-cycle variation
in HRS and LRS, which translates into read-margin loss, tail bits,
and the need for expensive program-and-verify schemes to hit a
target conductance.

The most direct attack on this in the folder is **Park et al. 2024**
(s41467-024-46682-1): an Al2O3 / TiO2 / TiOx trilayer engineered to
switch in *bulk* rather than via a filament. The key claims at p1-3:
(1) the porous, amorphous TiOx layer eliminates the high-diffusivity
grain-boundary paths that drive filament formation in crystalline
TiO2; (2) HRS/LRS resistance scales linearly with cell area, the
signature of bulk switching (Fig. 3b); (3) the device is *forming-
free* and can be programmed to up to 100 distinct conductance levels
without compliance current (Fig. 4); (4) all process steps are
< 300 C, "perfectly compatible with CMOS BEOL integration" (p2).
Endurance is reported as 2x10^5 cycles (Supplementary Fig. S4).

The trade-off is that Park 2024 is a passive-crossbar research demo
on a thermal-oxide silicon test wafer - it has not been integrated
into a CMOS 1T1R cell or qualified for production.

### Forming-free operation

Forming requires a higher voltage than normal SET (e.g., Niu 2016
forming voltage ~3.2-4.0 V vs. SET ~0.8-1.1 V; Niu 2016 Fig. 2).
That extra voltage headroom is incompatible with advanced-node logic
Vdd, so forming usually requires a dedicated charge pump and a
sequence of careful pulse trains. Eliminating the forming step (as
in Park 2024) is a research priority because it simplifies the
peripheral circuitry and removes a yield gate at wafer test.

### Selectorless / 1S1R crossbars

Achieving 4F^2 cell density (vs. ~6-12 F^2 for 1T1R) requires
removing the select transistor and replacing it with a two-terminal
selector. Lee 2011 (nmat3070 in folder, Fig. 3) demonstrates one
clever in-folder approach: the asymmetric Ta2O5-x / TaO2-x stack has
an *intrinsic* Schottky barrier that suppresses HRS leakage current,
so the device acts as its own selector. Other approaches use
chalcogenide ovonic threshold switches (OTS), NbO2 Mott-IMT
switches, or Ag-based threshold switches. None of these are in the
folder; the source guide does not have a dedicated selector
reference and one will need to be added if the paper covers 1S1R
in depth.

### Compact modeling for RRAM

For a MOSFET-modeling course, this is the most directly course-
relevant frontier. Two compact models dominate the literature:

- **Stanford-PKU model**: Jiang et al., "A compact model for
  metal-oxide resistive random access memory with experiment
  verification," *IEEE Trans. Electron Devices* 63, 1884 (2016). DOI
  10.1109/TED.2016.2545412. Source guide entry 20. Code:
  https://nano.stanford.edu/downloads/stanford-rram-model . The
  direct analog of BSIM for filamentary HfO2 / TaOx.
- **VTEAM**: Kvatinsky et al., "VTEAM: A general model for
  voltage-controlled memristors," *IEEE TCAS-II* 62, 786 (2015). DOI
  10.1109/TCSII.2015.2433536. Source guide entry 21. Simpler, easier
  to reproduce in SPICE.

If the paper includes a SPICE simulation - even a single 1T1R
read/write transient using one of these models - that is the
strongest possible "this is a MOSFET-modeling project" deliverable.

### In-memory computing and the neuromorphic pivot

The most active commercial frontier is using RRAM crossbars to do
matrix-vector multiplication directly in the memory array, exploiting
Ohm's law and Kirchhoff's current law. This bypasses the von Neumann
bottleneck for AI inference workloads and opens a market RRAM can
plausibly win without competing with DRAM or NAND on cost-per-bit.

Park 2024 is itself an in-memory-computing paper - it deploys the
trilayer RRAM crossbar as a spiking neural network for an autonomous
F1 racetrack inference task (Park 2024 Figs. 5-6, p5-6). The
applications-motivation reference is **Ielmini & Wong 2018** (cited
above).

### Integration at FinFET and GAA nodes

The TSMC 12FFC FinFET demo (IEDM 2023) is the most advanced-node
embedded RRAM result currently citable. Beyond FinFET, the GAA
(gate-all-around nanosheet) generation - Samsung 3 nm GAE/GAP, TSMC
N2 - is research-stage for RRAM. Embedded MRAM is currently ahead of
embedded RRAM at GAA. **No GAA-RRAM paper is in the folder yet, and
the source guide does not point to one; this is an open citation gap
if the paper covers GAA scaling.**

### Reliability physics: the tightest course tie-in

The forming step in HfO2 RRAM is electrically a *soft dielectric
breakdown* of the same HfO2 used as the high-k gate dielectric in
HKMG MOSFETs. The reliability physics - voltage-driven bond breaking,
percolation models, Arrhenius retention - is shared with MOS gate-
oxide TDDB. The single best reference for this bridge:

- **Ielmini, "Resistive switching memories based on metal oxides:
  mechanisms, reliability and scaling," *Semicond. Sci. Technol.* 31,
  063002 (2016). DOI 10.1088/0268-1242/31/6/063002.** Source guide
  entry 4. Treats filament formation as electrostatics-plus-
  activation and retention as Arrhenius. **Not yet read directly -
  pull this paper before writing the reliability section.**

Niu 2016 also makes this connection implicitly: its C-impurity hard-
breakdown analysis (Fig. 8, p7-8) is the same hard-breakdown framework
used for MOS gate oxides, and Niu cites Cho et al. 2013 on impurities
in MOS gate dielectrics (Niu 2016 ref. 51-52, p9).

---

## 5. The four PDFs in the folder, condensed

Brief reference card for the four PDFs. Detailed first-pass analysis
lives in the Git history of this file before the 2026-05-02 rewrite.

- **Niu 2016 (HfO2 / srep28155).** Process-aware case study of a
  0.25 um CMOS-integrated 1T1R HfO2 cell using batch ALD. Strongest
  "fab-to-device" link in the folder. Use as the HfO2 anchor and as
  the 0.25 um starting point in the scaling arc. Key numbers:
  forming ~3.2-4.0 V, SET ~0.8-1.1 V, RESET ~1.0-1.5 V, ON/OFF ~7-10,
  endurance ~300 dc cycles, NMOS select W=1.14 um L=0.24 um, Vg=0.9 V
  for Icc=10 uA, sintering at 400 C, RRAM placed between metal 2
  and metal 3 (Niu 2016 Methods, p9).
- **Torrezan 2011 (TaOx / Nanotechnology 22).** Pure device-physics
  speed-ceiling paper. Single device on a high-resistivity Si /
  thermal SiO2 wafer with 200 nm Pt CPW lines, *not* a CMOS chip.
  Key numbers: 105 ps SET / 120 ps RESET, 1.9 / 5.8 pJ switching
  energies, +2.0 V / -3.3 V pulses, 7 nm TaOx / Ta(30 nm)/Pt(200 nm)
  TE / Ti(5 nm)/Pt(20 nm) BE.
- **Lee 2011 (TaOx / nmat3070).** Engineered Ta2O5-x / TaO2-x
  asymmetric bilayer with built-in Schottky selector. e-beam-defined
  30x30 nm^2 crossbars on Pt lines, *not* CMOS-integrated. Key
  numbers: >10^12 endurance at 30x30 um^2 (Fig. 2c), 10 ns pulse
  switching with Vset=-4.5 V Vreset=+6 V, 10-yr retention at 85 C
  with Ea=1.47 eV, 64-of-64 cell yield in a small array. Use as the
  TaOx endurance / scaling anchor.
- **Park 2024 (filament-free trilayer / s41467-024-46682-1).** Bulk-
  switching, forming-free, all-ALD-low-temperature trilayer for in-
  memory computing. Passive crossbar research demo, not 1T1R-
  integrated. Key numbers: MOhm regime, up to 100 conductance levels,
  endurance 2x10^5, all process steps < 300 C, F1-racetrack SNN
  hardware demo.

> Honest gap call: of the four, only Niu 2016 is on a real CMOS
> wafer. The "CMOS integrability" claim at the paper level needs
> additional foundry references (Chou 2018, IEDM 2023 12FFC, Weebit
> materials).

---

## 6. The MOS-modeling spine: the select-FET design problem

The course relevance of the project is concentrated in this section.
The 1T1R cell *requires* a MOSFET as the select device, and that
MOSFET is a real design problem that changes character at every node.
The paper can spend a section walking through it.

Sub-questions the paper can address, all of which are MOSFET problems:

- **Sizing for compliance current.** Forming HfO2 needs ~10-50 uA at
  the cell. The select FET in saturation has to deliver that at the
  Vds left after the RRAM voltage drop. This is a load-line analysis.
  Niu 2016 sets Vg = 0.9 V to give Icc = 10 uA on a planar bulk NMOS
  (W = 1.14 um, L = 0.24 um) (Niu 2016 p2, p9). Reproduce that
  calculation as the paper's worked example.
- **High-voltage IO devices.** SET voltages of 1.5-2 V at the cell
  exceed the core-logic Vdd at advanced nodes (~0.8 V at 7 nm). The
  select FET is therefore a thick-oxide IO transistor, not a core
  logic transistor. That itself is a different MOSFET design.
- **Subthreshold leakage and array size.** Off-state leakage of
  unselected cells limits the array size. FinFET helps - steeper
  subthreshold = lower leakage = bigger arrays.
- **Fin quantization at FinFET nodes.** W is no longer continuous;
  it is integer fins x fin pitch. Sizing for a target Icc may force
  rounding to the next fin count. Genuinely unique to FinFET-era 1T1R.
- **FD-SOI back-gate trim.** At 22FDX, biasing the back-gate shifts
  the select-FET Vt, allowing per-die compliance-current trim. Unique
  to FD-SOI and is one of the cleanest reasons FD-SOI deserves its
  own subsection.
- **Parasitic R vs. RRAM resistance.** At advanced nodes, contact and
  via resistance approach the LRS resistance. Park 2024's MOhm-regime
  LRS is partly a response to this; it decouples the FET from the
  parasitic-R problem.

---

## 7. Foundry / node landscape (reference table)

Useful reference for the integration section. The four MOSFET
architectures the integration story passes through:

1. **Planar bulk** (250 nm down to 28 nm).
2. **Planar FD-SOI** (28FDSOI Samsung, 22FDX GlobalFoundries).
3. **FinFET** (TSMC 16FF onwards including 12FFC; Samsung 14LPP onwards;
   Intel 22FFL onwards).
4. **GAA nanosheet** (Samsung 3GAE/GAP, TSMC N2, Intel 18A).

TSMC node lineup with architecture:

  0.25um, 0.18um, 0.13um, 90nm, 65nm, 40nm  (planar bulk)
  28nm 28HPC/HKC  (planar bulk + HKMG; workhorse eRRAM node)
  22ULP/ULL  (planar bulk)
  16FF / 16FFC / 16FF+  (FinFET)
  12FFC  (FinFET, 16-class derivative)
  10nm, N7/N7+, N5/N5P, N4, N3/N3E  (FinFET)
  N2, A16  (GAA nanosheet)

Note: "12FFC = FinFET", not planar. The "12" is marketing; the process
is a 16FF derivative. Anything with "FF" in TSMC's name is FinFET.

Foundries to know for embedded RRAM:
- **TSMC**: leading-edge logic; eRRAM at 40/28/22/12FFC.
- **Samsung Foundry**: leading-edge logic; eMRAM more mature than
  eRRAM in their offering.
- **GlobalFoundries**: stopped at 12 nm; specializes in 22FDX (FD-SOI).
  Hosts the Weebit FD-SOI demo.
- **SkyWater**: smaller US foundry, flagship 130 nm planar bulk;
  hosts the Weebit 130 nm ReRAM product and runs the open-source
  SKY130 PDK.
- **Intel Foundry Services, UMC, SMIC, Tower**: secondary or legacy
  for this story.

RRAM IP companies (no fab):
- **Weebit Nano** (Israel): TaOx ReRAM IP, partners SkyWater 130 nm
  and GF 22FDX.
- **Crossbar Inc.** (US): filamentary OxRAM IP, more standalone focus.
- **Adesto / Renesas**: CBRAM, shipped at 130/180 nm.

---

## 8. Open / TODO

- [ ] Verify the per-foundry eMRAM availability claims in Section 3.5
  (Samsung 28FDSOI, TSMC 22/16/12, GF 22FDX). Currently propagated
  from industry-press knowledge. Likely primary sources are IEDM /
  ISSCC / VLSI Symposium 2018-2024 papers from each foundry plus
  Samsung Foundry and GF web materials.
- [ ] Pull the TSMC 12FFC IEDM 2023 handout (URL in section 3 above).
- [ ] Find a primary citation for "TSMC eFlash stops at 28 nm." Try
  TSMC Technology Symposium proceedings 2018-2019; backstop with
  Hellenbrand 2024.
- [ ] Find a primary GF 22FDX + Weebit ReRAM IEDM/VLSI Symposium
  paper to cite in the FD-SOI section.
- [ ] Confirm Wong & Salahuddin 2015 (Nat. Nanotech. 10, 191) and
  pull the PDF; this is the keystone "memory leads computing"
  reference and the single most-needed primary source.
- [ ] Pull Ielmini 2016 SST 31, 063002 to write the reliability /
  TDDB-bridge section.
- [ ] Pull Ielmini & Wong 2018 Nature Electronics for the in-memory-
  computing motivation in Section 4.
- [ ] Pull Wong et al. 2012 *Proc. IEEE* 100, 1951 (source guide
  entry 3) - the comparison-table mainstay.
- [ ] Decide whether to drop the Park 2024 trilayer or the Lanza h-BN
  as the "novel" slot. Current lean: keep Park 2024 (in folder, lines
  up cleanly with the variability narrative), name h-BN once for
  context only.
- [ ] Decide whether the paper includes an actual SPICE simulation
  using Stanford-PKU or VTEAM. If yes, this becomes the strongest
  course-relevance deliverable.
- [ ] Decide whether to include CBRAM at all. Source guide flags its
  absence as the largest folder gap. Minimum patch is Zahoor 2022.
  A short CBRAM paragraph in Section 1 is probably the right scope.

---

## 9. Citation hygiene

Throughout this file:

- Claims tied to a paper in the folder are cited "Author Year (paper
  short name in folder)" plus a section/figure pointer where useful.
- Claims tied to a paper *not* in the folder but listed in the source
  guide carry a full DOI plus the source-guide entry number, and are
  flagged "**Not yet read directly**" if I have not personally
  verified them.
- Claims that come only from the original source-guide annotations
  (rather than from a paper I have read) are explicitly marked as
  such. **Verify before quoting in the final paper.**

For the chat-side answers the user asked for, every strong claim
should be marked with one of:

- **(Niu 2016 ...)** for HfO2 ALD process / 0.25 um CMOS / 1T1R
- **(Torrezan 2011 ...)** for sub-ns TaOx, 100 ps SET / 120 ps RESET
- **(Lee 2011 ...)** for TaOx asymmetric bilayer, 1e12 endurance,
  30 nm crossbar, intrinsic Schottky selector
- **(Park 2024 ...)** for trilayer bulk switching, MOhm regime, 100
  conductance levels, < 300 C BEOL
- **(source guide entry N)** for everything else (TSMC nodes, CBRAM,
  Weebit, Lanza, compact models, industry roadmaps, Wong & Salahuddin
  2015, Hellenbrand 2024)
