---
title: "Combat"
date: 2024-11-17
---

Here's the info I know about combat in Betrayal at Krondor.

# Combat data in the save game

Different aspects of combat data are distributed in the save game in a number of locations. These include:

* [Combat Entity Lists](#combat-entity-lists)
* [Combat Planned Times](#combat-planned-times)
* [Combatant Grid Locations](#combatant-grid-locations)
* [Combatant Skills](#combatant-skills)
* [Combatant Inventories](#combatant-inventories)
* [Combatant World Locations](#combatant-world-locations)

Combats are identified by a number I call `CombatIndex`. This index is used in the encounter for a combat to reference the specific combat. The index can then be used to link to the `CombatEntityList`.

## Combat Entity Lists

The combat entity lists provide a mapping from `CombatIndex` to the individual combatants that participate in the combat. This can be empty, for example for traps, which are also a type of combat and are treated in the same manner by the game.

The combat entity list is defined in my engine as:
```cpp
class CombatEntityList
{
public:
    std::vector<CombatantIndex> mCombatants;
};
```

For example, the first few combat entity lists are:
```
#0 CombatEntityList{0,1,2}
#1 CombatEntityList{3}
#2 CombatEntityList{4,5}
```

Note that the `CombatIndex` is zero indexed. However the 0th combat does not seem to actually be present anywhere in the game. The 1st combat is the single moredhel warrior just south of LaMut. 

The `CombatantIndex` is, as the name suggests, a number that can be used to link to information for individual combatants. It links to their combat grid locations, their inventories, and their skills.

## Combat Planned Times

When clicking on a combatant the party will attempt to ambush the enemy. This marks the combat as having been ambushed. When the combat encounter is actually triggered the game will check the ambushed flag and roll a die to see if the party actualy successfully ambushed the enemies. If they did then the party gains the initiative and hence the first move in combat.

Ambush chance will expire after 30 in-game minutes. The intention here seems to be to encourage players to trade off planning/ambushing a combat as soon as it is seen (low probability of success), versus trying to get as close to the combat as possible without triggering it in order to shorten the time between ambushing and triggering combat. If the combat is triggered after ambushing and within 30 minutes, a random number between 0 and 100 will be selected. If it is less than the lowest stealth skill in the party the ambush will be considered successful and the party will gain initiative in the ensuing combat.

These times are indexed by `CombatIndex`. For combat #1, after clicking, we see the following:
```
#1 Time {2 days 14:31 (1b792)}
```

## Combat Grid Locations

Combatant grid locations specify, for a particular combatant, their `MonsterIndex` (i.e. what type of monster they are), and the grid position that they should occupy in the combat. These are indexed by the `CombatantIndex`.

I define these as:
```cpp
class CombatantGridLocation
{
public:
    MonsterIndex mMonster;
    glm::uvec2 mGridPos;
};
```

The combatant grid location for combatant #3, which is the only combatant in combat #1, is:
```
#3 CombatantGridLocation{ Monster: 18 pos: uvec2(2, 6)}
```

We can verify this by looking at the BaKHelpWeb entry for this combat: [Combat #1](https://xavieran.github.io/BaKHelpWeb/notendur.hi.is/eybjorn/krondor/com1-01.html)

## Combatant Skills

For each combatant, their skill values, spells, and any active conditions are defined. These are the same type of attributes as for the player character, although obviously the non combat skills (Barding, Lockpicking, Scouting, etc.) are unused by enemies. These are indexed by `CombatantIndex`.

For Combatant #3 these are:
```
Health { Max: 27 TrueSkill: 27 Current: 27 Experience: 0 Modifier: 0[ ] [ ]}
Stamina { Max: 21 TrueSkill: 21 Current: 21 Experience: 0 Modifier: 0[ ] [ ]}
Speed { Max: 3 TrueSkill: 3 Current: 3 Experience: 0 Modifier: 0[ ] [ ]}
Strength { Max: 14 TrueSkill: 14 Current: 14 Experience: 0 Modifier: 0[ ] [ ]}
Defense { Max: 40 TrueSkill: 40 Current: 40 Experience: 0 Modifier: 0[ ] [ ]}
Crossbow { Max: 0 TrueSkill: 0 Current: 0 Experience: 0 Modifier: 0[ ] [ ]}
Melee { Max: 46 TrueSkill: 46 Current: 46 Experience: 0 Modifier: 0[ ] [ ]}
Casting { Max: 0 TrueSkill: 0 Current: 0 Experience: 0 Modifier: 0[ ] [ ]}
Assessment { Max: 210 TrueSkill: 210 Current: 210 Experience: 0 Modifier: 0[ ] [ ]}
Armorcraft { Max: 0 TrueSkill: 0 Current: 0 Experience: 0 Modifier: 0[ ] [ ]}
Weaponcraft { Max: 18 TrueSkill: 18 Current: 18 Experience: 0 Modifier: 0[ ] [ ]}
Barding { Max: 192 TrueSkill: 192 Current: 192 Experience: 0 Modifier: 0[ ] [ ]}
Haggling { Max: 88 TrueSkill: 88 Current: 88 Experience: 0 Modifier: 0[ ] [ ]}
Lockpick { Max: 255 TrueSkill: 255 Current: 255 Experience: 0 Modifier: 0[ ] [ ]}
Scouting { Max: 60 TrueSkill: 60 Current: 60 Experience: 0 Modifier: 0[ ] [ ]}
Stealth { Max: 58 TrueSkill: 58 Current: 58 Experience: 0 Modifier: 0[ ] [ ]}
```

## Combatant Inventories

Similarly to the combatant skills above, the combatants inventory is indexed by `CombatantIndex`. This contains all the items a combatant is carrying. This container will be accessed and items removed when looting a combatant's corpse.

For Combatant #3 we have:
```
#3 GenericContainer{ ContainerHeader @460b7 {CCL { arr: 64 0a 00 00 combat: 1 person: 0} LocType: 7 Items: 3 Capacity: 3 Flags: 0} Inventory {
#0 -> Standard Kingdom Armor #48 pct/qty: 76 status: 96 mods: [] IsEquipped: 1

#1 -> Broadsword #18 pct/qty: 63 status: 96 mods: [] IsEquipped: 1

#2 -> Silver Royals #54 pct/qty: 42 status: 0 mods: [] IsEquipped: 0

}}

```

The combat inventories are indexed by the `CombatIndex` (`combat: 1`) and the index of the combatant within the combat (`person: 0`). This allows us to search for specific inventories based on the `CombatIndex` and the combatant within the combat rather on the `CombatantIndex` alone.

## Combatant World Locations

Combatant world locations specify, for a particular combatant, their location and state in the world. The location is the (x, y) coordinate in the BaK world coordinate system, along with a heading, i.e. what direction the combatant is facing. There is also information on the state of the combatant. I define this in my engine as:

```cpp
class CombatWorldLocation
{
public:
    GamePositionAndHeading mPosition;
    std::uint8_t mImageIndex;
    std::uint8_t mState;
};
```

The image index determines which variant of the combatant image will be displayed. Each direction the combatant can face has 3 images with slight differences that simulate movement. This value is used as on offset on the base direction image and changes as the combatant moves.

The state flag denotes the state of the combatant. I am not 100% sure on the meaning of all the values of the state, but observation and disassembly has shown the following:

| Value | Meaning |
| -     | -       |
| 0     | Invisible |
| 1     | Invisible |
| 2     | Moving |
| 3     | Moving |
| 4     | Dead |

Invisible combatants are not displayed in the world view and hence cannot be clicked on, so cannot be scouted. Moving combatants will move along a route as the player advances time in the game. Dead combatants will obviously be displayed as corpses and can be looted.

The combat world locations are not populated in the save until the tile with the combat encounter is loaded, and their initial values are taken from the combat encounter definitions. They are indexed based on [encounter](#combat-definitions-in-the-encounters-file) dependent values. The offsets are calculated as
```
combatWorldLocationIndex = encounter.mTileIndex * 35 + encounter.mIndex * 7 + relativeCombatantIndex
```

Where the relative combatant index is the index of the combatant within the combat, i.e. ranging from 0 to 6.

For combat #1 we can see how the combatant world location changes as we progress time:
```
#70 CombatWorldLocation{ { pos: uvec2(42584, 11098), angle: 192} ImageIndex: 5 State: 3}
#70 CombatWorldLocation{ { pos: uvec2(42984, 11098), angle: 192} ImageIndex: 6 State: 3}
#70 CombatWorldLocation{ { pos: uvec2(43384, 11098), angle: 192} ImageIndex: 1 State: 3}
#70 CombatWorldLocation{ { pos: uvec2(43784, 11098), angle: 64} ImageIndex: 0 State: 3}
#70 CombatWorldLocation{ { pos: uvec2(43384, 11098), angle: 64} ImageIndex: 5 State: 3}
#70 CombatWorldLocation{ { pos: uvec2(42984, 11098), angle: 64} ImageIndex: 6 State: 3}
#70 CombatWorldLocation{ { pos: uvec2(42584, 11098), angle: 64} ImageIndex: 1 State: 3}
```
As he moves the coordinates change and the image index is updated.

The combat world locations are cleared when moving to a new zone. A consequence of this is that if you have not looted gear from a dead combatant when leaving a zone you will not be able to when reentering it.

# Combat definitions in the encounters file

Combat is triggered in Betrayal at Krondor by the encounter type `COMBAT` or `TRAP`. The data for these can be found in the files `DEF_COMB.DAT`, and `DEF_TRAP.DAT` respectively. These files contain a list of combat encounters. The combat encounter defines which combat to trigger (i.e. the `CombatIndex`), the dialog to be shown on entry to the combat, the dialog to be shown when scouting the combat, the positions the party should take when retreating from a combat, whether the combat is an ambush or not, and per-combatant information.

The per-combatant information includes the monster type (`MonsterIndex`) in order to know what monster to display in the world, the starting location of the combatant, and how the combatant moves through the world.

For our combat #1, this looks like:
```
Combat { #1 Entry: 14 Scout: 0 
Trap: [[null]] 
North: { pos: uvec2(44255, 25787), angle: 128} 
West: { pos: uvec2(30531, 16283), angle: 192} 
South: { pos: uvec2(40028, 903), angle: 0} 
East: { pos: uvec2(49542, 3172), angle: 0} unknown: 0 isAmbush: 0 
Combatants: [{ Monster: 18, MoveType: 1, { pos: uvec2(42184, 11098), angle: 64}}, ]
```

We can see that the position of the combatant corresponds to one of the observed positions in the world location of the combatant.

I've defined this in the engine as:
```cpp
class Combat
{
public:
    unsigned mCombatIndex;
    KeyTarget mEntryDialog;
    KeyTarget mScoutDialog;

    std::optional<GamePositionAndHeading> mTrap;
    GamePositionAndHeading mNorthRetreat;
    GamePositionAndHeading mWestRetreat;
    GamePositionAndHeading mSouthRetreat;
    GamePositionAndHeading mEastRetreat;

    std::vector<CombatantData> mCombatants;
    std::uint16_t mUnknown;
    // ambushes are not visible, but are scoutable
    // not ambushes are visible, but not scoutable
    bool mIsAmbush;
};

struct CombatantData
{
    std::uint16_t mMonster;
    std::uint16_t mMovementType;
    GamePositionAndHeading mLocation;
    // path...
};
```
