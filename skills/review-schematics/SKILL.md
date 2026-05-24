---
name: schematic-to-device-tree
description: >
  Use this skill whenever the user wants to review a hardware schematic PDF and
  produce a comprehensive Markdown document covering all components, pinouts,
  interfaces, DNP parts, design issues, and hardware guideline compliance. Triggers
  include: "review schematic", "check schematic", "schematic review",
  "hardware review", or any mention of analyzing a PCB or circuit schematic PDF.
  Always auto-discovers PDFs in the current directory and a ./Datasheet folder for
  reference datasheets — never ask the user to specify paths unless nothing is found.
---

# Schematic Review & Table Generation Skill

Performs a thorough review of hardware schematic PDFs and produces a single
Markdown document containing all component details, interface tables, DNP impact
analysis, schematic issues, hardware guideline compliance notes, and a general
notes section.

- **Architecture Analysis**: Understand project structure, identify core
  components, trace subsystems
- **Component Inspection**: Query any component by reference, get
  connectivity, neighbors, properties
- **Net Tracing**: Follow signal paths across pages, find connected components
- **Bus Detection**: Identify I2C/SPI/UART/USB buses via pattern matching
- **Design Review**: Check power domains, interface modes, unconnected pins

---

## Core Principle

```
Accuracy first, efficiency second.
Choose mode first, query what's needed for reliable conclusion.
Structure first, semantics when blocked.
```

## Iron Rule

**Accuracy and evidence override coverage. Every claim requires direct evidence of the matching type.**

If evidence does not support a conclusion:
- Return `Unknown` or a lower-confidence result
- State which evidence is present and missing
- Avoid inventing roles or meanings to make output look complete

**No evidence, no assertion; weak evidence, weak conclusion.**

Each claim type demands specific evidence — a device's power domain requires checking its VDD/VCC pin, an interface mode requires tracing all signal lines to their endpoints, not just some. Inferences from device type, connector names, page location, or neighboring components are not sufficient on their own.

## Step 1 — Discover Files

### 1a. Find schematic PDFs
```bash
find . -maxdepth 2 -iname "*.pdf" | sort
```
If multiple PDFs are found, list them and ask the user which to process unless they
said "all". If none are found, tell the user.

### 1b. Find reference datasheets
```bash
find ./Datasheet -iname "*.pdf" 2>/dev/null | sort
```
Load datasheets for every IC found in the schematic during review. Cross-reference
recommended decoupling, pin configurations, operating conditions, and application
circuit guidelines against what is in the schematic.

---

## Step 2 — Read Every Page of the Schematic

Schematics are visual. Rasterize every page — do not rely on text extraction alone.

### 2a. Quick diagnostic
```bash
pdfinfo <file>.pdf
pdftotext -f 1 -l 1 <file>.pdf - | head -20
```

### 2b. Rasterize all pages at 200 DPI
```bash
pdftoppm -jpeg -r 200 <file>.pdf /tmp/sch_page
ls /tmp/sch_page-*.jpg      # filenames vary with page count — always list first
```

**For every page, extract exhaustively:**
- Title block: board name, revision, date, author
- All ICs and chips: reference designator, part number, description
- All passive components: resistors, capacitors, inductors, ferrite beads with value,
  tolerance, voltage/current rating, footprint note if visible
- All connectors: reference designator, type, pin count, signal per pin, voltage
- All test points and fiducials
- All net names and labels, especially power rails
- Bus interfaces: I2C (address, pull-up), SPI (CS mapping, speed), UART (RX/TX
  relative to SoC), USB, CAN, Ethernet, MIPI, LVDS, etc.
- GPIO: signal name, direction, pull resistor, active polarity, default state
- Clock sources, crystal specs, oscillator enables
- Reset circuits: supervisor ICs, RC timing, active polarity
- Interrupt lines and which IC asserts them
- **DNP (Do Not Populate) components**: note every DNP explicitly — see Step 4

### 2c. Text extraction (cross-check)
```python
import pdfplumber
with pdfplumber.open("<file>.pdf") as pdf:
    for i, page in enumerate(pdf.pages):
        text = page.extract_text()
        if text:
            print(f"=== Page {i+1} ===\n{text}\n")
```

---

## Step 3 — Build the Markdown Document

Produce **one Markdown file** with all sections below. Include every section;
write "None found" if a section has no data rather than omitting it.

---

### 3.1 — Document Header

```markdown
# Schematic Review: <Board Name> <Revision>

**File:** <filename.pdf>  
**Review Date:** <date>  
**Pages Reviewed:** <N>  
```

---

### 3.2 — ICs and Chips

One row per IC. Include every semiconductor — MCU, SoC, PMIC, sensor, transceiver,
memory, gate driver, op-amp, comparator, LDO, load switch, logic gate, etc.

| Ref | Part Number | Description | Interface | Address / CS | Supply Voltage | Datasheet Found | Notes |
|-----|-------------|-------------|-----------|--------------|----------------|-----------------|-------|

**Datasheet Found**: Yes / No — check `./Datasheet/` folder.

---

### 3.3 — Passive Components

Group by type. Do not skip any passive — every R, C, L, FB matters for review.

#### Resistors
| Ref | Value | Tolerance | Power Rating | Net(s) | Function | DNP | Notes |
|-----|-------|-----------|--------------|--------|----------|-----|-------|

#### Capacitors
| Ref | Value | Voltage Rating | Type (MLCC/Elec/Tant) | Net(s) | Function | DNP | Notes |
|-----|-------|----------------|-----------------------|--------|----------|-----|-------|

#### Inductors / Ferrite Beads
| Ref | Value / Impedance | Current Rating | Net(s) | Function | DNP | Notes |
|-----|-------------------|----------------|--------|----------|-----|-------|

---

### 3.4 — Connectors

| Ref | Part / Type | Pin Count | Mating / Pitch | Pin # | Signal | Direction | Voltage | Notes |
|-----|-------------|-----------|----------------|-------|--------|-----------|---------|-------|

---

### 3.5 — Power Rails

| Net Name | Voltage | Source (IC Ref) | Load(s) | Bulk Cap | Decoupling Cap | Max Current | Notes |
|----------|---------|-----------------|---------|----------|----------------|-------------|-------|

---

### 3.6 — Interfaces

One sub-table per bus type present. Omit bus types not found.

#### I2C
| Bus | Pin | Signal | Pull Resistor | Pull Rail | Connected ICs (Address) | Speed | Notes |
|-----|-----|--------|---------------|-----------|-------------------------|-------|-------|

#### SPI
| Bus | Pin | Signal | Direction | CS Target (Ref) | Max Frequency | Notes |
|-----|-----|--------|-----------|-----------------|---------------|-------|

#### UART / Serial
| Instance | Pin | Signal | Direction | Baud | Flow Control | Connected To | Notes |
|----------|-----|--------|-----------|------|--------------|--------------|-------|

#### GPIO
| Pin | Signal | Direction | Pull | Active Level | Default State | Connected To | Notes |
|-----|--------|-----------|------|--------------|---------------|--------------|-------|

#### Other Buses (USB / CAN / Ethernet / MIPI / etc.)
| Bus Type | Pin | Signal | Direction | Connected To | Notes |
|----------|-----|--------|-----------|--------------|-------|

---

### 3.7 — Clocks and Oscillators

| Ref | Type (XTAL/OSC/PLL) | Frequency | Load Cap / Accuracy | Connected To | Enable Signal | Notes |
|-----|---------------------|-----------|---------------------|--------------|---------------|-------|

---

### 3.8 — Reset and Supervisory Circuits

| Ref | Part / Type | Threshold Voltage | Delay | Active Polarity | Asserts Reset On | Notes |
|-----|-------------|-------------------|-------|-----------------|------------------|-------|

---

### 3.9 — Test Points and Debug Headers

| Ref | Signal | Voltage | Type (TP/Header) | Notes |
|-----|--------|---------|------------------|-------|

---

## Step 4 — DNP Components and Impact Analysis

List **every DNP component**. For each, state what feature or circuit it enables or
disables when populated.

| Ref | Part / Value | Circuit / Net | DNP Effect (when populated) | Dependency |
|-----|-------------|---------------|------------------------------|------------|

**DNP Impact Notes:** After the table, add a short paragraph describing any
functional modes that change depending on DNP population — e.g. "Populating R12
switches the I2C address of U4 from 0x48 to 0x49" or "C22 DNP disables soft-start
on U3; board will have hard power-on ramp."

---

## Step 5 — Schematic Issues

Review the schematic critically against the extracted data and datasheets. Flag
every issue found, no matter how minor. Categorize each issue:

- **Critical** — will prevent the board from functioning or cause damage
- **Major** — likely to cause unreliable operation or failed bring-up
- **Minor** — suboptimal but probably functional; should be corrected in next spin
- **Warning** — worth monitoring; no immediate action required

| # | Severity | Ref(s) | Issue Description | Recommended Fix |
|---|----------|--------|-------------------|-----------------|

**Examples of things to look for:**
- Missing or wrong-value decoupling capacitors (compare to datasheet recommendations)
- Floating input pins on ICs
- Pull-up/pull-down on wrong rail (e.g. 5V pull-up on 3.3V-only IO)
- I2C pull-up value too high for target speed
- Power sequencing violations (compare to IC startup requirements)
- Missing series termination on high-speed lines
- Incorrect crystal load capacitors
- Net naming inconsistencies (same signal, different names on different pages)
- Testpoint missing on key signals
- DNP component creates ambiguous net state when unpopulated

---

## Step 6 — Hardware Guideline Compliance

For each IC with a datasheet in `./Datasheet/`, rasterize the relevant application
circuit and recommended layout sections, then compare to the schematic:

```bash
pdftoppm -jpeg -r 150 ./Datasheet/<part>.pdf /tmp/ds_page
ls /tmp/ds_page-*.jpg
```

| IC Ref | Part | Guideline | Schematic Value / Config | Status | Notes |
|--------|------|-----------|--------------------------|--------|-------|

**Status values:** ✅ Compliant · ⚠️ Deviation · ❌ Non-compliant · ➖ Not checked (no datasheet)

---

## Step 7 — Notes

Free-form section for observations that do not fit elsewhere:
- Board-level design intent assumptions made during review
- Signals or nets that could not be traced fully
- Recommendations for next revision
- Questions for the hardware designer
- Anything else worth preserving

---

## Step 8 — Write Output File

Save the completed document to the current working directory:
```bash
# Save alongside the schematic PDF
./schematic_review.md
```
Then call `present_files`.

---

## Pre-Output Checklist

- [ ] Every page of the schematic rasterized and viewed
- [ ] All ICs captured with part number, supply voltage, and datasheet lookup
- [ ] All passives (R, C, L, FB) captured with value and function
- [ ] All connectors with full pin mapping
- [ ] All power rails with source, loads, and decoupling
- [ ] All bus interfaces (I2C addresses, SPI CS, UART direction, GPIO polarity)
- [ ] All clock sources and crystal specs
- [ ] All reset/supervisory circuits
- [ ] All test points and debug headers
- [ ] All DNP components listed with impact analysis
- [ ] Schematic issues section completed (even if empty — write "No issues found")
- [ ] Hardware guideline compliance checked for all ICs with datasheets
- [ ] Notes section populated