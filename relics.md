# Ancient Dungeon VR Modding Guide: Creating Custom Relics

Welcome to the modding guide for Ancient Dungeon VR (ADVR)! This guide will help you create your own custom relics for the game, using examples from existing relics. Relics are defined by Lua scripts and associated assets. Below you'll find instructions, examples, and important tips for modders.

---

## 1. Folder Structure & File Types

A typical relic folder contains:

- `your_relic_name.lua` — The main Lua script defining the relic's logic.
- `your_relic_name.png` — The icon or sprite for the relic.

**Example:**
```
items/
  my_custom_relic/
    my_custom_relic.lua
    my_custom_relic.png
```

## 2. Use the official ADVR Modding Tools extension for VSCode

Install the official [ADVR Modding Tools VSCode extension](https://marketplace.visualstudio.com/items?itemName=erthugames.advr-modding-tools) — it provides linting and autocompletion for all modding types. We strongly recommend using it to make creating ADVR mods easier. 

---

## 3. Example 1: Basic Mod

Each relic script must define certain functions and properties. Here is a minimal example:

The `ADVR.onLoad()` function will setup the relic in the game. It also defines if a relic can be used in multiplayer or if it is shared. 

```lua
function ADVR.onLoad()
    pickup.name = "Ginseng Root"
    pickup.desc = "Gain 5 Insight"
    pickup.probability = 0.6
    pickup.maxAmount = 1
    pickup.amountUses = 1
    pickup.price = 50
    pickup.tier = 3
    pickup.spawnsIn = {"goldenChest", "shop"}
    pickup.supportedInMultiplayer = true
end
```

The `ADVR.onPickup()` function is called in the moment the player picks up the relic. Here you can add the logic for your custom relic. 

```lua
function ADVR.onPickup()
    pickup.RegisterItem()
    player.Insight = player.Insight + 5
end
```

---

## 4. Example 2: Changing Player Stats for all players

The `ADVR.onLoad()`sets up a shared multiplayer supported relic. 
```lua
function ADVR.onLoad()
    pickup.name = "Burlap Blindfold"
    pickup.desc = "Drastically increases all your characteristics, but relics will not longer be identifiable in advance"
    pickup.probability = 0.66
    pickup.maxAmount = 1
    pickup.amountUses = 1
    pickup.price = 60
    pickup.tier = 3
    pickup.spawnsIn = {"special"}
    pickup.supportedInMultiplayer = true
    pickup.isSharedItem = true
    game.RegisterForRPCEvents(pickup)
end
```

In `ADVR.onPickup()`, we register the relic in the game. Since we are creating a shared relic, we must also implement `ADVR.onPickupProxies()`.
```lua
function ADVR.onPickup()
    pickup.RegisterItem()
    player.PrimaryDamage.RegisterMultiplier(pickup.id, 2)
    player.SecondaryDamage.RegisterMultiplier(pickup.id, 2)
    player.PrimaryCritChance.RegisterAddend(pickup.id, 0.15)
    player.SecondaryCritChance.RegisterAddend(pickup.id, 0.15)
    player.EvasionChance.RegisterAddend(pickup.id, 0.3)
    player.ShopDiscount.RegisterAddend(pickup.id, 0.15)
    HidePickupStats()
end
```

In `ADVR.onPickupProxies()`, we register the same state changes as for the host, but for all the proxies this time.
```lua
function ADVR.onPickupProxies(originalPlayerRef)
	pickup.RegisterItemNoSync()
	player.PrimaryDamage.RegisterMultiplier(pickup.id, 2)
	player.SecondaryDamage.RegisterMultiplier(pickup.id, 2)
	player.PrimaryCritChance.RegisterAddend(pickup.id, 0.15)
	player.SecondaryCritChance.RegisterAddend(pickup.id, 0.15)
	player.EvasionChance.RegisterAddend(pickup.id, 0.3)
	player.ShopDiscount.RegisterAddend(pickup.id, 0.15)
    HidePickupStats()
end
```

In `HidePickupStats()`, we loop through all relics that have not been picked up yet and hide their description and name. Additionally, we set the sprite to null, rendering the relic as a question mark.

```lua
function HidePickupStats()
	AllActivePickups = game.itemInterpreter.GetActivePickupsFromAllPlayers()
	for pick in game.itemInterpreter.pickupsOnDisk do
		if not IsStringInList(AllActivePickups, pick.id) then
			pick.sprite = nil
			game.localizationInterpreter.currentlySelectedLanguage.SetTranslation(pick.id .. "_desc", "???")
			game.localizationInterpreter.currentlySelectedLanguage.SetTranslation(pick.id .. "_name", "???")		
		end
	end
end
```

## 5. Example 3: Pickup That poisens Enemies

This a more complex example showing how to interact with different components of the game.

The `ADVR.onLoad()`sets up a non shared multiplayer supported relic. 

```lua
function ADVR.onLoad()
	pickup.name = "Miasmic Toadskin"
	pickup.desc = "Poison a random enemy when entering a room for the first time"
	pickup.probability = 1.0
	pickup.maxAmount = 3
	pickup.amountUses = -1
	pickup.price = 15
	pickup.tier = 1
	pickup.spawnsIn = {"podest", "shop", "goldenChest"}
	pickup.supportedInMultiplayer = true
    pickup.isSharedItem = false
end
```

In `ADVR.onPickup()`, we simply register the relic in the game. This is a common use case for relics that don't change the game when the player picks them up, but rather change the game later when another event occurs.

```lua
function ADVR.onPickup()
	pickup.RegisterItem()
end
```

`ADVR.onRoomEntered()` is called every time the player enters a new room. Therefore, it is perfectly suited for our relic, where we simply filter the room types and then call the main logic of the relic.

```lua
function ADVR.onRoomEntered(room)
	if (room.type == roomTypes.ENEMY or room.type == roomTypes.CORRIDOR or room.type == roomTypes.SECRET or room.type == roomTypes.ITEM) then
		pickup.CallFunctionIn("PoisonRandomEnemyInRoom", 1.0, room)
	end
end
```

The main logic of this relic is implemented by the function `PoisonRandomEnemyInRoom()`. For example, we use the function `game.GetEnemiesInRadius()` to retrieve all enemies within a sphere around the player. Then, we simply check if the enemy is in the room that the player is currently in. If so, we poison the enemy via the function `enemy.applyEffect(damageType.POISON, player.PrimaryDamage.GetValueFloat() / 2, 8)`. Sounds can be played via `audio.PlaySoundLocal()` or `audio.PlaySoundNetwork()` if we want all players to hear the sound. Finally, there is logic that rewards the player for picking up the relic multiple times.

```lua
function PoisonRandomEnemyInRoom(room)
	local counterPoisendEnemies = 0
	local enemies = game.GetEnemiesInRadius(32, game.playerHeadset.position, true, true)
	if #enemies > 0 then
		for enemy in enemies do
			local roomOfEnemy = game.currentWorldGenerator.GetRoomAtRealPos(enemy.transform.position)
			if room == roomOfEnemy then
				enemy.applyEffect(damageType.POISON, player.PrimaryDamage.GetValueFloat() / 2, 8)
				audio.PlaySoundLocal(sounds.SFX_MIASMIC_TOADSKIN, enemy.transform.position)
				counterPoisendEnemies = counterPoisendEnemies + 1
				if counterPoisendEnemies >= pickup.AmountActiveLocal() then
					return
				end
			end
		end
	end
end
```

---

## 6. Getting Help

- Check the official ADVR modding Discord or forums for more examples and support.
- Look at other relic scripts for inspiration.
- Use the VSCode extension for ADVR.

Happy modding!