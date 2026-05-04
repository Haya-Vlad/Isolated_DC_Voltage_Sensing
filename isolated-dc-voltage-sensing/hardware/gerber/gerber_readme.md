# Gerber Files

This folder contains the PCB fabrication files exported from Eagle CAD.

## File Naming Convention (Eagle default)

| File Extension | Layer Description       |
|----------------|------------------------|
| `.GTL`         | Top copper layer        |
| `.GBL`         | Bottom copper layer     |
| `.GTS`         | Top soldermask          |
| `.GBS`         | Bottom soldermask       |
| `.GTO`         | Top silkscreen          |
| `.GBO`         | Bottom silkscreen       |
| `.GKO`         | Board outline (keepout) |
| `.DRL`         | Drill file (Excellon)   |
| `.TXT`         | NC drill file           |

## Recommended PCB Specs

| Parameter         | Value             |
|-------------------|-------------------|
| Layers            | 2                 |
| Board thickness   | 1.6 mm            |
| Copper weight     | 1 oz (35 µm)      |
| Surface finish    | HASL or ENIG      |
| Min trace width   | 6 mil             |
| Min drill size    | 0.3 mm            |
| Solder mask       | Green (both sides)|
| Silkscreen        | White (top side)  |

## Ordering

Upload the contents of this folder as a `.zip` to your PCB manufacturer.  
Tested with: JLCPCB, PCBWay.
