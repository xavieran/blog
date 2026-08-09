---
title: Coordinate/Tiled World System (WIP)
---

Betrayal at Krondor's coordinate system revolves around a concept of `zones`, `tiles`, and `cells`.

There are 9 outdoor zones and 3 underground zones. Zones are made up of tiles. The individual tiles provide a way for the game to load and unload information as tiles move into and out of range of the player. At any one point in time only a few tiles will be loaded, but the transitions between them are very smooth, giving the illusion of a larger world.

There is a concept of `cells` which are smaller subdivisions of tiles. They provide an optimisation to store positions with less information and are also used for road movement alignment, when in "Follow Road" mode, the player may only move between the centers of cells. Underground "fog of war" is also on a per-cell basis. BaK also uses tile relative positions which are not cells, but provide an absolute world position relative to a tile. These are only used for combatant coordinates (CONFIRM THIS).

Coordinates in Betrayal at Krondor are cartesian (0,0) is bottom-left, or southern and westernmost. Positive x coordinates grow right/east, and positive y coordinates grow up/north.

The exact units for each subdivision are specified in the table below.

| Subdivision | Size |
| - | - |
| Zone | N/A |
| Tile | 64000 |
| Cell | 1600 |
| Combat Cell | 300 |


To obtain an absolute world position from a tile and a cell, we can use the following computation:
```
position = Tile * 64000 + (Cell * 1600 + (1600 / 2))
```
This returns the center of the cell.

[Encounters]({{ site.baseurl }}/content/encounters.html) span from the edges of cells, when the player crosses into a cell covered by an encounter, the encounter is triggered.

In order to obtain an absolute world position from a tile and a tile relative position, we use simple addition:
```
position = Tile * 64000 + TileRelativePosition
```

Rotation angles are stored as a 16 bit quantity representing the angle around a full circle. They can be normalised to radians with:

```
radians = (BakAngle / 0xffff) * 2 * pi
```

0 is North, 64 << 8 is West, 128 << 8 is South, 196 << 8 is East.
