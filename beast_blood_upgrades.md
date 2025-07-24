# Ancient Dungeon VR Modding Guide: Creating Custom Beast Blood Upgrades

Welcome to the modding guide for Ancient Dungeon VR (ADVR)! This guide will help you create your own custom Beast Blood Upgrades for the game, using real examples from existing upgrades. Upgrades are defined by Lua scripts and associated assets, and are located in the `progress_shops/blacksmith_*/` folders. Below you'll find instructions, examples, and important tips.

## 1. Folder Structure & File Types

You can use the ADVR Extension to add a valid Beast Blood upgrade to your mod folder by pressing <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> and selecting `Create New ADVR Beast Blood Upgrade`. 

A typical Beast Blood upgrade folder contains:

- `your_beast_blood_upgrade_name.lua` — The main Lua script defining the upgrade's logic.
- `your_beast_blood_upgrade_name.png` — The icon or sprite for the upgrade.

**Example:**
```
progress_shops/blacksmith_*/
    my_custom_beast_blood_upgrade.lua
    my_custom_beast_blood_upgrade.png
```

## 2. Example video

https://github.com/user-attachments/assets/8c6c05f2-3f4a-4325-9d28-fcc9b4e8f5a9

---

