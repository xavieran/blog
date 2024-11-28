---
title: Encounters (WIP)
---

# Encounters

As you travel through the world of Betrayal at Krondor at times the party will experiences `encounters`. These might be things like a combat, a dialog with an NPC, or being able to access a city or town. I call these events `encounters`. BaK has a relatively sophisticated encounter system that places these events in the game world and decides when to trigger them for players.

There are nine types of encounter in the game, below I have enumerated their type, the number used to refer to them in the game code, the file the definitions can be found in, and their purpose.

| Type | Number | File | Purpose |
| - | - | - | - |
| [Background](#background) | 0 | DEF_BKGR.DAT | An adventure game screen, e.g. the waterfall near Cavall Keep, or the Oracle of Aal |
| [Combat](#combat) | 1 | DEF_COMB.DAT | Enters combat |
| [Comment](#comment) | 2 | no file (would be DEF_COMM.DAT) | Mentioned in code, but no comment files exist |
| [Dialog](#dialog) |  3 | DEF_DIAL | Begins a dialog, this could be with an NPC (Squire Phillip), or just a short flavour dialog |
| [Health](#health) | 4 | no file (would be DEF_HEAL.DAT) | Mentioned in code, but no health files exist |
| [Sound](#sound) | 5 | no file (would be DEF_SOUN.DAT) | mentioned in code, but no sound files exist |
| [Town](#town) | 6 | DEF_TOWN.DAT | Adventure game screen, but always towns or cities, e.g. Lamut, or Krondor |
| [Trap](#trap) | 7 | DEF_TRAP.DAT | Almost identical to combat, but includes trap elements |
| [Zone](#zone) | 8 | DEF_ZONE.DAT | Transitions between zones |
| [Disable](#disable) | 9 | DEF_DISA.DAT | Disables a game state flag |
| [Enable](#enable) | 10 | DEF_ENAB.DAT | Enables a game state flag |
| [Block](#block) | 11 | DEF_BLOC.DAT | A dialog encounter that blocks further progress, e.g. dialog with Finn in Chapter 1 when trying to enter Northlands from Tyr Sog |

# Encounter definitions

Encounters are defined on a per tile basis. Please refer to the [coordinate](/combat/coordinates.md) system for more information on tiles.

Each tile may have a file named `TXXYY.DAT`. This contains a list of encounters that may be present in that tile. These are defined on a per chapter basis, so some encounters might be present on that tile only in chapter 1, but not in chapter 3. (e.g. Squire Phillip NPC location changes between chapters).
