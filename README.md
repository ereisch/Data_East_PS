# Data_East_PS
Data East pinball power supply board, replacement for 520-5047-02

## Rationale

A short while ago, I became frustrated with the replacement power supply options available for Data East pinball machines. The factory original ones had several key deficiencies as I saw them:
 - Manufacturing of the PCB was rather poor, with many traces delaminating easily from the substrate
 - There was no overvoltage protection on the 5V logic supply (the main CPU board is rather expensive to replace, and is powered exclusively by this board)
 - 5V supply is incredibly inefficient, requiring a massive heatsink on the switching transistor to keep it healthy
 - Bridge rectifier is hard-soldered to the board, making replacement difficult (this part is known to fail)
 - 12V rails are unregulated; although this appears to be an explicit design choice by the OEM, it results in an audible 50/60Hz hum from the audio system
 - General illumination relay is noisy and prone to contact wear

Additionally, I didn't like many aftermarket supplies:
 - Rottendog is stable, but significantly under-caps the -12V rail for unknown reasons; this makes the audio system hum 50x worse
 - Most have 0.093" connectors are soldered to the board; these are stiff pin & socket connectors and you really stress the substrate when plugging/unplugging them
 - XPin doesn't appear to separate their rails (they say issues on 12V rails can propagate back to the 5V bus); I can't tell for sure because they don't publish the schematic for their board (as is their right not to)
 - Cost; most aftermarket replacements are over $150, but a BOM for one of these is usually less than $20, plus the cost of the PCB

So, I set out to build my own, open-source, higher-efficiency version. Since many older machines are transitioning to LED displays, the high-voltage section is a part-for-part copy from the original schematic (I'm using TFTC's schematics, as they're the highest resolution I could find on IPDB). The 5V, +/-12V, and GI control sections are redesigns for efficiency, simplicity, or serviceability, and will be adding crowbar circuits to each for protection from HV display faults.  Once complete, I will post Gerbers and a BOM shopping cart to do a complete build. Using kicad version 8.0.8.

## Specifications

Gathering all my research into this section.

+5V output on the stock supply is limited to 5.5A.  This was ascertained from the MC1723 datasheet, which specifies the limiting current via the R<sub>sense</sub> (R6) resistor value, which is 0.12 ohms in the -02 version. The equation is: I<sub>sc</sub> = 0.66 / R<sub>sense</sub>

Power limits of the HV section isn't as clear from the components (all are well oversized), but is limited to a combined ~45W between the 90 and 100V legs of the input transformer, due to fuse F7. Also, the voltage tolerances on this section aren't as stringent as the delta-V between them, since they aren't driving logic, but a VFD. As long as the high end of the voltages don't exceed the HV shift registers, any damage done will be long-term damage to the vacuum display itself.

### Transformer Analysis

*Note: all of this was analyzed using the 10-5003-00 in my Jurassic Park system.*

Power limits of the transformer: Unfortunately, these are undocumented from what I've been able to find. However, we can infer *minimum* limits from the factory fusing specifications on the various systems that utilized it:

#### Primary Windings

| Pair Colors | Inductance | Q-Factor | Resistance |
|-------------|------------|----------|------------|
| Black / Brown | 15.107 mH | 11.00 | 8.628 &Omega; |
| Black / Orange | 18.673 mH | 10.57 | 11.079 &Omega; |
| White-Black / White-Brown | 15.159 mH | 10.75 | 8.840 &Omega; |
| White-Black / White-Orange | 18.636 mH | 10.64 | 11.025 &Omega; |

#### Secondary Windings

| Pair Colors | Inductance | Q-Factor |Resistance | Voltage (AC) | Power Delivery (min) | Usage |
|-------------|------------|----------|-----------|---------|--------------------|-------|
| Yellow-White / Yellow | 55.7 &mu;H | 5.6 | 0.060 &Omega; | 6V | 120W | General Illumination |
| Grey-White / Grey | 138.9 &mu;H | 8.42 | 0.103 &Omega; | 9V | 63W | Logic, Sound & Motors |
| Grey-White / Grey-Green | 139.3 &mu;H | 8.79 | 0.099 &Omega; | 9V | 63W | Logic, Sound & Motors |
| Grey-Green / Grey | 558.3 &mu;H | 11.78 | 0.299 &Omega; | | | |
| Blue-White | 277.9 &mu;H | 9.02 | 0.194 &Omega; | 13V | 104W | Lamps |
| White-Red | 1148.2 &mu;H | 12.05 | 0.595 &Omega; | 25V | 200W | Solenoids |
| Black-Yellow | 3.908 mH | 11.89 | 2.074 &Omega; | 48V | 240W | Solenoids |
| White-Green / White | 11.664 mH | 8.07 | 9.035 &Omega; | 90V | ? <45W | VFD |
| Black-Red / White | 17.855 mH | 8.40 | 13.361 &Omega; | 100V | ? <45W | VFD |

*Note all measurements were at 1 Khz*

*The 9VAC outputs are a bit of an anomaly - they are wired as a center-tapped pair of 9VAC RMS each. The primary purpose of these taps are to drive the +5V logic and +/- 12V sound/accessory rails. On power supply revisions 02 and up, CN7 outputs a half-wave rectified version of each of the 9VAC taps to pins 4 and 5 as a weak (and noisy) -12VDC rail. It does not appear to be used on any of the 6 games that shipped with this supply revision.

For a "115V" input (i.e. 110-120V), both primary sides of the transformer are wired in parallel.  The "center" taps for each primary appear to be at about 80%, which (when used) will give a small output voltage boost for primary voltages lower than the standard 115/230V. For a 230V primary, the primaries are wired in series.

On my Jurassic Park machine, the "9V" taps measured 10.63V for a 122.3V primary.

*Source for most of my analysis: Jurassic Park & Tales From The Crypt Schematics*
