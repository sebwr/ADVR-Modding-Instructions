# Ancient Dungeon VR Modding Guide: Creating Custom Insight Upgrades

Welcome to the modding guide for Ancient Dungeon VR (ADVR)! This guide will help you create your own custom insight upgrades for the game, using real examples from existing upgrades. Insight upgrades are defined by Lua scripts and associated assets, and are located in the `progress_shops/acolyte/` folder. Below you'll find instructions, examples, and important tips.


## 1. Folder Structure & File Types

You can use the ADVR Extension to add a valid insight upgrade to your mod folder by pressing <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> and selecting `Create New ADVR Insight Upgrade`. 

A typical insight upgrade folder contains:

- `your_insight_upgrade_name.lua` — The main Lua script defining the upgrade's logic.
- `your_insight_upgrade_name.png` — The icon or sprite for the upgrade.

**Example:**
```
progress_shops/acolyte/
    my_custom_insight_upgrade.lua
    my_custom_insight_upgrade.png
```

## 2. Example video


---

