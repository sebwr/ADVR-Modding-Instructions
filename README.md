# Ancient Dungeon VR Modding Guide: Create Base mod

## Getting started

We highly recommend using our new [VSCode ADVR extension](https://marketplace.visualstudio.com/items?itemName=erthugames.advr-modding-tools) in combination with VSCode. All of the following tutorials assume that you are using VSCode and have the extension installed.

## Create your Mod

You can use the ADVR Extension to create a valid mod skeleton by pressing <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> and selecting `Create New ADVR Mod`. The Dialog asks then for an name of your mod and creates the necessary folder structure for you. The crucial `mod.modinfo` file is located inside the root folder. This file should list the mod name, a short description of the mod, and the last ADVR game version with which the mod is compatible.

### Video Tutorial: Create Mode

https://github.com/user-attachments/assets/89a89f5f-c5f1-4a6f-8856-8bfc64cb3790

Now you are ready to create the actual content of your mod. Currently the following types of mods are supported in ADVR:

- [Achievements](achievements.md)
- [Challenges](challenges.md)
- [Relics](relics.md)
- [Orbs](orbs.md)
- [Insight upgrades](insight_upgrades.md)
- [Beast blood upgrades](beast_blood_upgrades.md)
- [Weapons](weapons.md)
- [Rooms](rooms.md) (ADVR Room modder needed...)
