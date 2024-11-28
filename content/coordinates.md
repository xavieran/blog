---
title: Coordinate/Tiled World System (WIP)
---

Betrayal at Krondor's coordinate system revolves around a concept of `zones`, `tiles`, and `cells`.

There are 9 outdoor zones and 3 underground zones. Zones are made up of tiles. The individual tiles provide a way for the game to load and unload information as tiles move into and out of range of the player. At any one point in time only a few tiles will be loaded, but the transitions between them are very smooth, giving the illusion of a larger world.

Finally, there is a concept of `cells`. Cells are effectively smaller subdivisions of tiles, and as far as I can tell are just an optimisation to store positions with less information. BaK also uses tile relative positions which are not cells, but provide an absolute world position relative to a tile. These are only used for combatant coordinates (CONFIRM THIS).

Coordinates in Betrayal at Krondor are cartesian (0,0) is bottom-left, or southern and westernmost. Positive x coordinates grow right/east, and positive y coordinates grow up/north.

The exact units for each subdivision are specified in the table below.

| Subdivision | Size |
| - | - |
| Zone | NA |
| Tile | 64000 |
| Cell | 1600 |


To obtain an absolute world position from a tile and a cell, we can use the following computation:

(this is not true in all cases)
```
position = Tile * 64000 + (Cell * 1600 + (1600 / 2))
```

In order to obtain an absolute world position from a tile and a tile relative position, we use simple addition:
```
position = Tile * 64000 + TileRelativePosition
```

Rotation angles are stored as a 16 bit quantity representing the angle around a full circle. They can be normalised to radians with:

```
radians = (BakAngle / 0xffff) * 2 * pi
```
