
# Ancient Dungeon VR Modding Guide: Creating Custom Orbs

Welcome to the modding guide for Ancient Dungeon VR (ADVR)! This guide will help you create your own custom orbs for the game, using real examples from existing orbs. Orbs are defined by Lua scripts and associated assets, and are located in the `potions/` folder. Below you'll find instructions, examples, and important tips for modders.

---

## 1. Folder Structure & File Types

A typical orb folder contains:

- `your_orb_name.lua` — The main Lua script defining the orb's logic.
- `your_orb_name.png` — The icon or sprite for the orb.

**Example:**
```
potions/
  my_custom_orb/
    my_custom_orb.lua
    my_custom_orb.png
```

## 2. Use the official ADVR Modding Tools extension for VSCode

Install the official [ADVR Modding Tools VSCode extension](https://marketplace.visualstudio.com/items?itemName=erthugames.advr-modding-tools) — it provides linting and autocompletion for all modding types. We strongly recommend using it to make creating ADVR mods easier. 

---


## 3. Example 1: Orb with Area Effect (Explosion)

Each orb script must define certain functions and properties. Here is a real example from `erthu_orb_of_obliteration.lua`:

The `ADVR.onLoad()` function sets up the orb in the game:

```lua
function ADVR.onLoad()
    potion.name = "Orb of Obliteration"
    potion.desc = "Throw to cause an explosion that deals massive damage"
    potion.probability = 0.05
    potion.effectTime = 0
    potion.price = 60
    potion.spawnsIn = { "goldenChest", "secret", "sacrifice" }
    potion.color = colors.Create(0, 0, 0, 1)
    potion.worksInMultiplayer = true;
    potion.createEffectInstance = false
end
```

The `ADVR.PotionEvents.onPotionBreak()` function is called when the orb breaks (is used). 

```lua
function ADVR.PotionEvents.onPotionBreak(potionObject, effectInstance, stateAuthority)
    if stateAuthority then
        local explosion = game.SpawnObjectNetwork(objects.EFFECT_EXPLOSION_BIG, potionObject.transform.position).GetComponent_Explosion_()
        explosion.damage = 100 + (player.PrimaryDamage.GetValueFloat() + player.SecondaryDamage.GetValueFloat()) * 4
    end
end
```

---


## 4. Example 2: Orb with Status Effect (Poison Cloud)

Here is a real example from `erthu_noxious_orb.lua`:

```lua
function ADVR.onLoad()
    potion.name = "Noxious Orb"
    potion.desc = "Throw to release a cloud of poisonous gas."
    potion.probability = 1.0
    potion.effectTime = 0
    potion.price = 15
    potion.spawnsIn = {"chest", "shop", "blackmarket", "sacrifice"}
    potion.color = colors.Create(53.0/255.0, 112.0/255.0, 26.0/255.0,1)
    potion.contactDamage = false
    potion.worksInMultiplayer = true;
    potion.createEffectInstance = false
end

function ADVR.PotionEvents.onPotionBreak(potionObject, effectInstance, stateAuthority)
    local cloud = game.SpawnObjectLocal(objects.EFFECT_POISONOUS_GAS, potionObject.transform.position)
    cloud.transform.localScale = vector3.__new(6, 6, 6)
    local dmgArea = cloud.GetComponent_DamageArea_()
    dmgArea.playerImmune = true
    if stateAuthority then
        dmgArea.enemiesImmune = false
        dmgArea.damage = (player.PrimaryDamage.GetValueFloat() + player.SecondaryDamage.GetValueFloat()) * 0.4
    else
        dmgArea.enabled = false
    end
end
```

---


## 5. Example 3: Orb with Buff/Debuff (Movement Speed)

Here is a real example from `erthu_flickering_orb.lua`. Special about this example is, that this is an orb which has an effect time and an effect instance. 

```lua
function ADVR.onLoad()
    potion.name = "Flickering Orb"
    potion.desc = "Throw at your feet to temporarily increase your movement speed."
    potion.probability = 1.0
    potion.effectTime = 20
    potion.price = 15
    potion.spawnsIn = {"goldenChest", "shop"}
    potion.color = colors.Create(3.0/255.0, 227.0/255.0, 252.0/255.0, 1.0)
    potion.affectsPlayer = true
    potion.affectsEnemies = false
    potion.affectsLiving = false
    potion.breakEffectDistance = 2
    potion.showActiveVignette = true
    potion.createEffectInstance = true
    potion.worksInMultiplayer = true;
end

function ADVR.PotionEvents.onPotionBreak(tmpPotion, effectInstance)
    if effectInstance.affectedPlayer ~= nil then
        player.MoveSpeed.RegisterAddend("erthu_flickering_orb", 0.8)
    end
end

function ADVR.PotionEvents.onPotionRunOut(effectInstance)
    if effectInstance.affectedPlayer ~= nil then
        player.MoveSpeed.RemoveAddendAt("erthu_flickering_orb", 0)
    end
end
```

---


## 6. More Advanced Example: Transforming Objects (Gold)

Here is a real example from `erthu_glittering_orb.lua`:

```lua
function ADVR.onLoad()
    potion.name = "Glittering Orb"
    potion.desc = "Throw to transform nearby enemies or objects in gold."
    potion.probability = 0.15
    potion.effectTime = 10
    potion.price = 15
    potion.spawnsIn = {"blackmarket", "sacrifice"}
    potion.color = colors.Create(350.0/255.0,350.0/255.0,70.0/255.0,1)
    potion.contactDamage = false
    game.dropTableHandler.CreateDropTable("midasPotion");
    game.dropTableHandler.AddToDropTable("midasPotion", "item_coin", 1, 1, 1, 15, 20)
    potion.createEffectInstance = true
    potion.worksInMultiplayer = true
    game.RegisterForRPCEvents(potion)
end

function ADVR.PotionEvents.onPotionBreak(tmpPotion, tmpEffectInstance, stateAuthority)
    if stateAuthority then
        local living = game.GetLivingInRadius(2, tmpPotion.transform.position)
        local objectsTurnedIntoGold = 0
        for v in living do
            if objectsTurnedIntoGold > 3 then break end
            if v ~= nil and v.gameObject ~= nil then
                -- check if potion --
                local itemPotion = v.gameObject.GetComponent_ItemPotion_()
                local enemy = v.gameObject.GetComponent_EnemyBase_()
                local canTurnToGold = itemPotion == nil and (enemy == nil or not enemy.isBoss())
                if canTurnToGold then
                    objectsTurnedIntoGold = objectsTurnedIntoGold + 1
                    potion.SendRPCEvent(0, vector3.zero, v.networkObject, rpcType.TO_ALL)
                end
            end
        end
    end
end
```

This orb uses an RPC event to transform objects and enemies into gold, change their material, and assign a custom drop table.

---

## 7. Getting Help

- Check the official ADVR modding Discord or forums for more examples and support.
- Look at other orb scripts for inspiration (see the `potions/` folder).
- Use the VSCode extension for ADVR.

Happy modding!
