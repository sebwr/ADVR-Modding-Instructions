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

https://github.com/user-attachments/assets/39242f19-1b0b-46b6-b98c-464837185685

---

## 3. Basic Acolyte Insight Upgrade

Here's a simple example from the acolyte shop that actively modifies game behavior:

```lua
-- gets run when the game loads for setup
function ADVR.onLoad()
	progress.name = "Relic Hunter"
	progress.desc = "<i>Undiscovered relics have a greater chance of appearing</i>"
	progress.price = 50
	progress.predecessor = "progress_i_have_a_system"
end

function ADVR.ProgressEvents.onBuy()
end

function ADVR.onPreDungeonGenerated(worldGenerator)
	-- increase probability of a few never found relics
	local amountRelicsToIncreaseProbability = 3
	local amountRelicsToIncreaseSpawnPool = 1
	for pick in game.itemInterpreter.pickupsOnDisk do
		if not pick.HasUnlockedItem() and not pick.HasFoundItem()  then
			if amountRelicsToIncreaseProbability > 0 then
				pick.probability = 1.0
				amountRelicsToIncreaseProbability = amountRelicsToIncreaseProbability - 1

				-- increase spawn pool for some of them
				if amountRelicsToIncreaseSpawnPool > 0 then
					pick.spawnsIn = {"shop", "special", "boss", "podest", "secret"}
					amountRelicsToIncreaseSpawnPool = amountRelicsToIncreaseSpawnPool - 1
				end
			end
		end
	end
end
```

This upgrade:
- Uses `ADVR.onPreDungeonGenerated()` to modify the world before it's created
- Increases the probability of undiscovered relics appearing
- Modifies spawn locations for some relics
- Demonstrates how to iterate through all pickups in the game

---

## 4. Blacksmith Weapon Upgrade

Here's an example from the blacksmith shops that modifies weapon behavior:

```lua
function ADVR.onLoad()
	progress.name = "Words of Vengeance"
	progress.desc = "Your spells damage is increased when striking abberrant or boss enemies"
	progress.price = 2
	progress.predecessor = progresses.PROGRESS_MANA_MASTERY
end

function ADVR.ProgressEvents.onBuy()
end

function ADVR.onMeleeHitEntity(infos)
	if infos.entity == nil then
		return infos.damage
	end

	if infos.entity.IsAbberrant() or infos.entity.IsBoss() then
		return infos.damage + 1
	end
	return infos.damage
end

function ADVR.onRangedHitEntity(infos)
	if infos.entity == nil then
		return infos.damage
	end

	if infos.entity.IsAbberrant() or infos.entity.IsBoss() then
		return infos.damage + 1
	end
	return infos.damage
end
```

This upgrade:
- Modifies both melee and ranged damage
- Uses conditional logic to check enemy types
- References a predecessor using the `progresses` table, if you are want to use your own upgrades just use strings. 
- Demonstrates damage modification through event handlers

---

## 5. Weapon Modification Upgrade

Here's an example that directly modifies weapon properties:

```lua
function ADVR.onLoad()
	progress.name = "Tethered Bolt"
	progress.desc = "You can retrieve your crossbow's bolt more quickly and from a greater distance."
	progress.price = 1
	progress.predecessor = nil
end

function ADVR.ProgressEvents.onBuy()
end

function ADVR.onDungeonGenerated(worldGenerator)
	game.inventory.currentPrimaryWeapon.AsCrossbowBase().pullbackDistance = 3.5
	game.inventory.currentPrimaryWeapon.AsCrossbowBase().pullbackSpeed = 12
end
```

This upgrade:
- Uses `ADVR.onDungeonGenerated()` to modify weapon properties after the dungeon is created
- Directly accesses and modifies weapon components
- Shows how to cast weapons to specific types (`AsCrossbowBase()`)
- Has no predecessor (can be bought immediately)

---

## 6. Getting Help

- Check the official ADVR modding Discord or forums for more examples and support.
- Look at other insight upgrade scripts in the `progress_shops/` folders for inspiration.
- Use the VSCode extension for ADVR to create new upgrades quickly.

Happy modding!

---

