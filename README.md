# 🔥 Pixel 6a Thermal Runaway vs. the "Safety Kit Shipping" Protocol

> **A technical case study in Android battery telemetry, thermal failure, and corporate bureaucracy.**
>
> Or: *How I learned that querying Linux kernel battery telemetry is apparently less authoritative than clearing the app cache.*

---

## ⚠️ Author's Note & Satirical Disclaimer

This document is a **satirical commentary piece**, written to preserve the author's sanity after weeks of navigating retail return policies, support deflections, and consumer-law bureaucracy.

The humour is intentional.

However, the technical evidence documented here is **genuine and was collected from the physical device**, including:

- Android/Linux kernel battery telemetry
- ADB diagnostic output
- Battery cycle count
- Android HAL battery-health state
- Learned battery-capacity values
- Thermal behaviour
- Charging restrictions
- Physical display symptoms

If you have a background in software engineering, Linux, embedded systems, Android development, or hardware diagnostics, hopefully you can appreciate the absurdity of watching raw hardware telemetry lose an argument with a customer-support script.

---

# 1. The Timeline of "Normal Wear & Tear"

For weeks, support maintained that a phone becoming extremely hot was consistent with ordinary battery degradation.

Some of the troubleshooting suggestions included:

### 🗑️ "Clear your app cache"

Because clearing temporary application files is, apparently, the traditional electrical-engineering solution to suspected lithium-ion battery failure.

### 💤 "Remove background apps"

Because terminating Android processes will presumably convince hardware reporting an abnormal battery-health state that everything is fine.

### 🌙 The Nightly Blackout

The phone could be left sitting unused on a cold nightstand, only to be discovered completely discharged and temporarily unresponsive to power input.

After approximately 30 minutes, it could become responsive again.

### 🤖 The Live Chat Panic Paradox

The support interaction reached an interesting state:

> **Please stop using the device immediately because it may be unsafe.**

Followed shortly by instructions effectively requiring the device to have sufficient charge for the return process.

Thus producing the fascinating state of:

**"Do not use this potentially unsafe battery-powered device. Now prepare that battery-powered device for transport."**

---

# 2. Low-Level Android Battery Telemetry

The following data was collected using ADB and Android/Linux system interfaces.

## Hardware Serial

```bash
adb shell getprop ro.serialno
```

---

## Battery Cycle Count

```bash
adb shell cat /sys/class/power_supply/battery/cycle_count
```

Output:

```text
468
```

---

## Android Battery Health State

```bash
adb shell dumpsys battery | grep -i "health"
```

Output:

```text
health: 4
```

The returned health value was therefore recorded directly from Android's battery service rather than inferred from an application.

---

## Learned Battery Capacity

```bash
adb shell dumpsys batterystats | grep -i "Capacity:"
```

Output:

```text
Estimated battery capacity: 3756 mAh
Last learned battery capacity: 3754 mAh
```

For comparison, the device's nominal battery specification is approximately **4,410 mAh**.

---

## Real-Time Battery Temperature

```bash
adb shell "cat /sys/class/power_supply/battery/temp | awk '{print \$1/10 \" °C\"}'"
```

This allows battery temperature to be monitored directly from the device's power-supply subsystem.

---

# 3. Telemetry Summary

| Metric | Expected / Nominal | Observed | Interpretation |
|---|---:|---:|---|
| Battery cycle count | `< 400` trigger reference | `468` | Device exceeded referenced cycle threshold |
| Battery health | `2` / GOOD | `4` | Abnormal battery-health state reported |
| Nominal capacity | `4,410 mAh` | `3,754 mAh` learned | Significant reduction in learned capacity |
| Charging | Up to `18 W` | Reduced during affected behaviour | Charging behaviour appeared restricted |
| Thermal behaviour | Stable during idle | Severe intermittent heat | Consistent with abnormal device behaviour |

> [!IMPORTANT]
> Raw telemetry is presented separately from interpretation wherever possible.  
> ADB output establishes what Android reported. Determining the exact electrochemical or hardware failure mechanism would require appropriate physical battery testing.

---

# 4. The Quantum Thermal State™

The device appeared to have discovered an exciting new branch of thermodynamics:

```text
Phone sitting idle on desk
        |
        +--> State A: Stone cold
        |    Sudden shutdown / temporarily unresponsive
        |
        +--> State B: Extremely hot
             Significant heat while apparently idle
```

There appeared to be no comfortable middle ground.

---

## 🔥 Unprovoked Heat Spikes

The device could become extremely hot while performing little or no intentional workload.

This behaviour occurred independently of ordinary troubleshooting steps such as clearing application caches or terminating background applications.

---

## 📱 OLED Pressure / Display Lines

During heat events, visible lines or pressure-like artefacts appeared across the OLED display near the battery area.

This was particularly concerning because the symptoms appeared alongside abnormal thermal behaviour.

Determining whether this resulted from battery expansion, thermal deformation, display pressure, or another internal hardware fault would require physical inspection.

---

# 5. The "Safety Kit" Shipping Paradox

And now we arrive at the masterpiece.

## Step 1: The Hazard

The phone is treated as sufficiently concerning that ordinary return transport is not considered appropriate.

Instead, a specialized **Safety Kit** is required.

Fair enough.

Lithium-ion battery incidents deserve caution.

## Step 2: The Paradox

The device must be handled using a special safety-oriented transport procedure...

…but its failure status supposedly cannot be established until it reaches the assessment process.

This produces the rather beautiful bureaucratic state:

> **The device is potentially hazardous enough to require special transportation, but not sufficiently defective to establish the remedy before assessment.**

---

## Step 3: Send the Unsafe Thing Somewhere Else

Conceptually, the process becomes:

```text
Potential battery safety issue
        |
        v
"STOP USING THE PHONE"
        |
        v
Special transport procedure required
        |
        v
Ship potentially unsafe device
        |
        v
Retail assessment
        |
        v
Possible manufacturer assessment
        |
        v
Confirmation of the problem already being investigated
```

Enterprise architecture has nothing on retail reverse logistics.

---

# 6. The Qualifications Paradox

At various stages, information surrounding Pixel 6a battery safety concerns existed from manufacturers, media reports, and consumer-safety channels.

Yet the retail process still required its own assessment before determining the applicable remedy.

This leads to another interesting hierarchy of diagnostic authority:

| Evidence | Bureaucratic Authority |
|---|---|
| Raw Android battery telemetry | 🤷 |
| Battery cycle counter | 🤷 |
| Abnormal HAL health state | 🤷 |
| Learned-capacity reduction | 🤷 |
| Abnormal thermal behaviour | 🤷 |
| Display artefacts | 🤷 |
| Software developer with ADB | ❌ |
| Retail assessment process | ✅ **THE ORACLE** |

---

# 7. Software Developer Regret

Years spent learning:

- C#
- ASP.NET
- Backend architecture
- Linux administration
- Server infrastructure
- Hardware diagnostics
- Android Debug Bridge
- Linux `/sys` interfaces
- Kernel and HAL concepts

...were apparently unnecessary.

True diagnostic enlightenment arrives only when somebody asks:

> **"Have you tried clearing the cache?"**

I have seen the light.

---

# 8. Australian Consumer Law Context

The dispute also raises questions under the **Australian Consumer Law (ACL)**, particularly the consumer guarantees relating to acceptable quality and remedies for major failures.

Relevant provisions include:

- **Section 54** — Guarantee as to acceptable quality
- **Section 259** — Consumer remedies against suppliers

Safety is particularly relevant when considering whether goods satisfy the guarantee of acceptable quality.

Whether a specific case legally constitutes a **major failure** depends on the facts and applicable consumer law, so this repository documents the technical evidence rather than pretending to be a court judgment.

The central argument is straightforward:

> A consumer device exhibiting serious unexplained thermal behaviour, shutdowns, abnormal battery-health telemetry, substantial capacity degradation, and possible physical display effects deserves considerably more investigation than "clear the cache."

---

# 9. The Complete Safety-Kit Workflow™

```text
Battery safety concerns become known
              |
              v
Android reports abnormal battery state
              |
              v
ADB telemetry is collected
              |
              v
Physical thermal/display symptoms documented
              |
              v
Support:
"STOP USING THE DEVICE"
              |
              v
Return requires special handling
              |
              v
Retail assessment still required
              |
              v
                    ┌──────────────────────────┐
                    │ Have you cleared cache? │
                    └──────────────────────────┘
```

---

# 10. Evidence

For reproducibility, useful diagnostic evidence can include:

```text
evidence/
├── adb/
│   ├── battery-health.txt
│   ├── battery-capacity.txt
│   ├── battery-cycles.txt
│   └── temperature-log.txt
│
├── photos/
│   ├── display-lines/
│   └── device-condition/
│
├── correspondence/
│   ├── manufacturer/
│   └── retailer/
│
└── README.md
```

> [!CAUTION]
> Do **not** intentionally stress, charge, heat, disassemble, puncture, or otherwise provoke a lithium-ion battery that may be damaged, swollen, overheating, or unsafe purely to collect diagnostic data.
>
> Personal safety is more important than obtaining another log file.

---

# 11. Final Note

Re-architecting backend software?

Easy.

Querying Linux kernel drivers?

Easy.

Debugging production infrastructure at 3 AM?

Annoying, but manageable.

Proving that a phone requiring specialized safety handling might have something more serious happening than an overflowing application cache?

**Apparently this is where computer science ends and the dark arts begin.**

---

## TL;DR

```text
Phone:      🔥
Android:    ⚠️
ADB:        ⚠️
Display:    ⚠️
Support:    DON'T USE IT
Shipping:   SAFETY KIT REQUIRED
Retail:     We need to assess whether there is a problem.
Developer:  ಠ_ಠ
```

---

## Disclaimer

This repository documents one device owner's observations, diagnostic output, correspondence, and experience.

It is:

- **not professional electrical-engineering advice**
- **not a battery repair guide**
- **not legal advice**
- **not an instruction to bypass Android safety systems**
- **not proof of the precise internal failure mechanism**

The satirical commentary represents the author's personal experience and opinion.

If a lithium-ion device is unusually hot, swollen, physically damaged, emitting unusual smells, smoking, or otherwise appears unsafe, stop using it and follow appropriate manufacturer or emergency-safety guidance.

---

*Made with ADB, Linux, questionable amounts of patience, and a newfound respect for the phrase "Safety Kit."*
