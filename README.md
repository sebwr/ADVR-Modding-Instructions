# Ancient Dungeon VR Modding Guide

## Getting started

We highly recommend using our new [VSCode ADVR extension](https://marketplace.visualstudio.com/items?itemName=erthugames.advr-modding-tools) in combination with VSCode. All of the following tutorials assume that you are using VSCode and have the extension installed.

### Video Tutorial: Install Extension

https://github.com/user-attachments/assets/55cb0577-a4d9-461a-836b-cd8f90af5aa4

Always check that the extension version matches the game version you are targeting; otherwise, the autocompletion may not reflect the latest game changes. After a successful installation, all commands via <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> should be available immediately, and autocompletion should also work out of the box.


## Create your Mod Base folder

You can use the ADVR Extension to create a valid mod skeleton by pressing <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> and selecting `Create New ADVR Mod`. The Dialog asks then for an name of your mod and creates the necessary folder structure for you. The crucial `mod.modinfo` file is located inside the root folder. This file should list the mod name, a short description of the mod, and the last ADVR game version with which the mod is compatible.

### Video Tutorial: Create Mod

https://github.com/user-attachments/assets/89a89f5f-c5f1-4a6f-8856-8bfc64cb3790

### Publishing / Shiping your Mod

Since ea0.1.8.0 your mod must be published as zip archive, which is then loaded from the ADVR_MODS folder on pc or quest. To create such an archive correctly you can also us the extennsion is VSCode. To create archive press <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> and select "Publish ADVR Mod". The extension will now automatically collect all the necessary files and compress them into an zip archive. This archive can then be easily shared with other players. 

## Create Content for your Mod

Now you are ready to create the actual content of your mod. Currently the following types of mods are supported in ADVR:

- [Achievements](achievements.md)
- [Challenges](challenges.md)
- [Relics](relics.md)
- [Orbs](orbs.md)
- [Insight upgrades](insight_upgrades.md)
- [Beast blood upgrades](beast_blood_upgrades.md)
- [Rooms](rooms.md)
- [Weapons](weapons.md)
