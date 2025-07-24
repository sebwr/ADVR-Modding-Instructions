# Ancient Dungeon VR Modding Guide: Creating Custom Achievements

Welcome to the modding guide for Ancient Dungeon VR (ADVR)! This guide will help you create your own custom achievements for the game, using real examples from existing achievements. Achievements are defined by Lua scripts and associated assets, and are located in the `achievements/` folder. Below you'll find instructions, examples, and important tips.

## 1. Folder Structure & File Types

You can use the ADVR Extension to add a valid achievement to you mod folder by pressing <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd> and selecting `Create New ADVR Achievement`. 

A typical achievement folder contains:

- `your_achievement_name.lua` — The main Lua script defining the achievement's logic.
- `your_achievement_name.png` — The icon or sprite for the achievement.

**Example:**
```
achievements/
    my_custom_achievement.lua
    my_custom_achievement.png
```

## 2. Example video

---