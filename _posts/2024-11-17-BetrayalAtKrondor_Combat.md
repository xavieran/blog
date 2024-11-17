---
title: "Betrayal at Krondor - Combat"
date: 2024-11-17
---

Here's the info I know about combat in Betrayal at Krondor.

# Combat data in the save game

Different aspects of combat data are distributed in the save game in a number of locations. These include:

* [Combat Entity Lists](#combat-entity-lists)
* [Combatant Grid Locations](#combatant-grid-locations)
* [Combatant World Locations](#combatant-world-locations)
* [Combatant Skills](#combatant-skills)
* [Combatant Inventories](#combatant-inventories)
* [Combat scouted times](#combat-scouted-times)

Combats are identified by a number I call `CombatIndex`. This index is used in the encounter for a combat to reference the specific combat. The index can then be used to link to the `CombatEntityList`.

## Combat Entity Lists

The combat entity lists provide a mapping from `CombatIndex` to the individual combatants that participate in the combat. This can be empty, for example for traps, which are also a type of combat and are treated in the same manner by the game.

The combat entity list is defined in my engine as:
```
class CombatEntityList
{
public:
    std::vector<CombatantIndex> mCombatants;
};
```

For example, the first combat in the game (i.e. with `CombatIndex` #1) has combat entity list:
```
Combat #0 0,1,2      
```

The `CombatantIndex` is, as the name suggests, a number that can be used to link to information for individual combatants. It links to the `CombatantWorldLocation`, individual combantant inventories and skills.

## Combat Grid Locations

Combatant grid locations specify, for a particular combatant, their `MonsterIndex` (i.e. what type of monster they are), and the grid position that they should occupy in the combat. These are indexed by the `CombatantIndex`.

I define these as:
```
class CombatantGridLocation
{
public:
    MonsterIndex mMonster;
    glm::uvec2 mGridPos;
};
```

The first 3 combatant grid locations, which as above are for the first combat in the game, are show below:
```
#0 CombatantGridLocation{ Monster: 53 pos: uvec2(1, 7)}
#1 CombatantGridLocation{ Monster: 53 pos: uvec2(4, 3)}
#2 CombatantGridLocation{ Monster: 29 pos: uvec2(3, 6)}
```

## Combatant World Locations

Combatant world locations specify, for a particular combatant, their location and state in the world. The location is the (x, y) coordinate in the BaK world coordinate system, along with a heading, i.e. what direction the combatant is facing. There is also information on the state of the combatant. I define this in my engine as:

```
class CombatWorldLocation
{
public:
    GamePositionAndHeading mPosition;
    std::uint8_t mUnknownFlag;
    std::uint8_t mState;
};
```

The state flag denotes the state of the combatant. I am not 100% sure on the meaning of all the values of the state, but observation and disassembly has shown the following:

| Value | Meaning |
| -     | -       |
| 0     | Invisible |
| 1     | Invisible |
| 2     | Moving |
| 3     | Moving |
| 4     | Dead |

Invisible combatants are not displayed in the world view and hence cannot be clicked on, so cannot be scouted. Moving combatants will move along a route as the player advances time in the game. Dead combatants will obviously be displayed as corpses and can be looted.

The combatant world locations are 

## Combatant Skills

For each combatant, their skill values are defined. These are the same type of attributes as for the player character, although obviously the non combat skills (Barding, Lockpicking, Scouting, etc.) are unused by enemies. These are indexed by `CombatantIndex`.

