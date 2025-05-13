# Data_East_PS
Data East pinball power supply board, replacement for 520-5047-02

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
