# Curios Forge Reference

## Source Priority

Prefer sources in this order:

1. Official Curios docs for the target Minecraft line, especially the 1.20.x docs when working on Forge 1.20.1.
2. Curios GitHub examples or source when docs omit method signatures.
3. Community tutorials, forum answers, and existing mod code only as pattern supplements.
4. Local project files and logs are the final arbiter for what is actually wired.

Important official documentation areas:

- Development environment setup
- Slot register
- Slot register data generation
- Entities
- Slot modifiers
- Slot types and preset slots
- Assigning items to slots
- Curio creation
- Curio predicates
- Curio rendering
- Accessing inventory
- Attribute modifiers
- Curios Attribute Modifiers (NBT), at `/1.20.x/curios/items/curios-nbt`
- Configuration

## Dependency Setup

Typical Forge projects need both build-time dependency wiring and runtime metadata:

- `build.gradle` needs a Curios dependency, often from the Illusive Soulworks Maven or CurseMaven.
- `mods.toml` should include a dependency on `curios` if the mod requires it.
- Version ranges should match the Minecraft line. For Forge 1.20.1, Curios 5.x is the common line.

CurseMaven-style dependency used by many Forge 1.20.1 workspaces:

```groovy
repositories {
    maven { url "https://cursemaven.com" }
}

dependencies {
    implementation fg.deobf("curse.maven:curios-309927:<file_id>")
}
```

When `gradle.properties` also declares `curios_version`, do not assume it is used; inspect `build.gradle`.

Example metadata shape:

```toml
[[dependencies.<your_modid>]]
modId="curios"
mandatory=true
versionRange="[5.0.0,)"
ordering="NONE"
side="BOTH"
```

Do not assume a Gradle dependency means Curios is loaded at runtime; always inspect logs or `mods.toml` when debugging.
Useful log proof looks like Curios being selected or loaded with a concrete version such as `5.14.1+1.20.1`.

## Slot Definition

A Curios slot type describes a named equipment slot such as `ring`, `necklace`, `charm`, or a custom slot like `method`.
Place custom slot definitions in the mod's data namespace:

```text
src/main/resources/data/<your_modid>/curios/slots/<slot_id>.json
```

The data namespace does not have to equal the code mod id as long as the paths and JSON are valid. For example, a mod with id `profundum_mod` can validly place Curios data under `data/curios_profundum/curios/...` while still tagging items as `profundum_mod:<item_id>`.

Common fields:

```json
{
  "icon": "<your_modid>:slot/<slot_id>",
  "order": 20,
  "size": 1,
  "use_native_gui": true
}
```

Notes:

- `icon` points to a texture under `assets/<namespace>/textures/<path>.png`; `"<modid>:slot/method_slot"` maps to `assets/<modid>/textures/slot/method_slot.png`.
- `size` controls how many slots exist for that slot type by default.
- `order` controls display ordering.
- `use_native_gui` lets Curios use the native GUI presentation when available.
- Many common slot names already exist as presets. Prefer a preset only when its semantics match the item; use a custom slot for domain concepts like cultivation methods, spell seals, or soul cores.

## Entity Slot Assignment

Defining a slot type is not always enough. Curios also needs to know which entity types get that slot.
Entity data normally lives under:

```text
src/main/resources/data/<your_modid>/curios/entities/<name>.json
```

Typical shape:

```json
{
  "entities": ["minecraft:player"],
  "slots": ["method"]
}
```

Use entity data when a custom slot does not appear on players despite valid slot JSON.
If the slot uses a preset or another mod already assigns it, entity JSON may not be needed, but explicit project-local JSON is often easier to reason about.

## Assign Items To Slots

Curios item eligibility is normally data-driven through item tags in Curios' namespace:

```text
src/main/resources/data/curios/tags/items/<slot_id>.json
```

Example:

```json
{
  "replace": false,
  "values": [
    "<your_modid>:enne_abysmal_pyro_alethia"
  ]
}
```

This namespace is the common pitfall. The slot can be defined in `data/<your_modid>/curios/slots`, but the item tag that tells Curios which items fit the slot usually belongs under `data/curios/tags/items`.

Use item tags for simple slot eligibility. Add Java only when the item needs behavior beyond "can be placed in this slot."

## Curio Item Behavior

Use `ICurioItem` or the version-appropriate Curios item API when an item needs:

- equip or unequip checks
- ticking while equipped
- attribute modifiers
- custom drop rules on death
- client rendering behavior
- NBT-sensitive validity
- side effects when inserted or removed

Common lifecycle concepts to look up in the versioned API:

- `canEquip` / `canUnequip`
- `curioTick`
- `onEquip` / `onUnequip`
- attribute modifier hooks
- drop rule hooks
- renderer hooks or renderer registration helpers

For simple items, do not implement `ICurioItem` unnecessarily. A plain `Item` plus the correct Curios tag is enough.

## Attribute Modifiers

For fixed bonuses such as attack damage +5 or max health +4, prefer `ICurioItem#getAttributeModifiers(SlotContext, UUID, ItemStack)` in Forge 1.20.1 / Curios 5.x. It is explicit, debuggable, and tied to the equipped Curios slot.

Example item that gives +5 attack damage and +4 max health when equipped:

```java
package your.mod.item;

import com.google.common.collect.HashMultimap;
import com.google.common.collect.Multimap;
import net.minecraft.world.entity.ai.attributes.Attribute;
import net.minecraft.world.entity.ai.attributes.AttributeModifier;
import net.minecraft.world.entity.ai.attributes.Attributes;
import net.minecraft.world.item.Item;
import net.minecraft.world.item.ItemStack;
import top.theillusivec4.curios.api.SlotContext;
import top.theillusivec4.curios.api.type.capability.ICurioItem;

import java.util.UUID;

public class StatCurioItem extends Item implements ICurioItem {
    public StatCurioItem(Properties properties) {
        super(properties);
    }

    @Override
    public Multimap<Attribute, AttributeModifier> getAttributeModifiers(
            SlotContext slotContext,
            UUID uuid,
            ItemStack stack) {
        Multimap<Attribute, AttributeModifier> modifiers = HashMultimap.create();

        modifiers.put(
                Attributes.ATTACK_DAMAGE,
                new AttributeModifier(
                        uuid,
                        "Curio attack damage bonus",
                        5.0D,
                        AttributeModifier.Operation.ADDITION));

        modifiers.put(
                Attributes.MAX_HEALTH,
                new AttributeModifier(
                        uuid,
                        "Curio max health bonus",
                        4.0D,
                        AttributeModifier.Operation.ADDITION));

        return modifiers;
    }
}
```

Notes:

- `Attributes.ATTACK_DAMAGE +5.0D` adds five attack damage.
- `Attributes.MAX_HEALTH +4.0D` adds four health points, which is two hearts.
- Use the `uuid` Curios passes into the method. It is slot-specific and prevents modifier duplication.
- Use `AttributeModifier.Operation.ADDITION` for flat bonuses. Multiplicative operations have different semantics and should be chosen deliberately.
- If health bonuses do not visually update immediately in testing, unequip/re-equip or heal the player; max health and current health are distinct.
- This Java behavior does not replace the item tag. The item still needs to be eligible for a Curios slot through `data/curios/tags/items/<slot>.json`.

Alternative: register behavior without subclassing the item:

```java
CuriosApi.registerCurio(ModItems.MY_CURIO.get(), new ICurioItem() {
    @Override
    public Multimap<Attribute, AttributeModifier> getAttributeModifiers(
            SlotContext slotContext,
            UUID uuid,
            ItemStack stack) {
        Multimap<Attribute, AttributeModifier> modifiers = HashMultimap.create();
        modifiers.put(Attributes.ATTACK_DAMAGE,
                new AttributeModifier(uuid, "Curio attack damage bonus", 5.0D,
                        AttributeModifier.Operation.ADDITION));
        return modifiers;
    }
});
```

Use this when the item class is shared or you want Curios behavior registered centrally.

Event route: `CurioAttributeModifierEvent` can add, remove, or replace modifiers after the item contributes its own modifiers. Use it for cross-cutting rules such as "all items in this slot gain a bonus under a condition" rather than item-local fixed stats.

NBT route: Curios also supports item NBT attribute modifiers through the official `Curios Attribute Modifiers (NBT)` docs. Use this for loot-generated or NBT-variable bonuses. For hand-written fixed mod items, Java hooks are usually clearer.

Curios NBT uses `CurioAttributeModifiers`, not vanilla `AttributeModifiers`:

```mcfunction
/give @s minecraft:netherite_chestplate{CurioAttributeModifiers:[{Slot:"body",AttributeName:"generic.attack_damage",Name:"body_attack_bonus",Amount:5.0,Operation:0},{Slot:"body",AttributeName:"generic.max_health",Name:"body_health_bonus",Amount:4.0,Operation:0}]} 1
```

NBT notes:

- `Slot` is the Curios slot id, such as `body`, `ring`, or `method`.
- `AttributeName:"generic.attack_damage"` gives attack damage.
- `AttributeName:"generic.max_health"` gives max health.
- `Operation:0` is flat addition.
- Curios slot count modifiers can also be represented through attributes named like `curios:back`, but use these deliberately because they change slot capacity, not player combat stats.

Slot count modifiers are different from entity attributes. For changing how many slots exist, use Curios slot modifier APIs such as `CuriosApi.addSlotModifier(...)` or the item-stack slot modifier helper, not `Attributes.MAX_HEALTH`.

## Inventory Access

Use Curios APIs to inspect or modify equipped Curios. Do not manually scan only vanilla equipment slots.
Look for helpers such as inventory capability access, equipped-curio search, or slot inventory retrieval in the installed API version.

Typical tasks:

- Check whether a player has a specific curio equipped.
- Find the first curio matching a predicate.
- Get the item stack in a named slot.
- Insert or remove a curio programmatically.
- Apply effects while an item is equipped.

When implementing gameplay logic, keep server-side authority in mind. Ticking effects, advancement triggers, and stat changes should normally run server-side; rendering and model work is client-side.

## Rendering

Curios rendering is separate from normal item model rendering.
An item texture or `.geo.json` does not automatically render on a player's body.

Use client-side renderer registration when:

- a curio should appear on the player model
- the item needs a custom body transform
- GeckoLib or another animation renderer is involved
- a model should follow head, body, arm, or leg movement

Debug rendering by separating these layers:

1. Does the item have a valid inventory model?
2. Can the item equip in its Curios slot?
3. Is a Curios renderer registered on the client?
4. Are model and texture resource paths valid?
5. Are animations supported by the chosen renderer, not just exported into assets?

## Data Generation

If a project already uses Forge data generation, prefer adding Curios data providers for slots, entities, tags, and modifiers.
Generated resources help avoid typos and keep slot ids centralized.

If the project is small or hand-written, direct JSON files are acceptable. Validate them with a JSON parser and check the effective paths.

## Debugging Checklist

### Slot does not appear

- Check `data/<modid>/curios/slots/<slot>.json`.
- Check `data/<modid>/curios/entities/*.json` includes `minecraft:player`.
- Check logs for data loading errors.
- Check Curios config, hidden slots, or disabled GUI behavior.
- Check whether the slot id collides with an existing slot with different settings.

### Item cannot be equipped

- Check `data/curios/tags/items/<slot>.json`.
- Check the item registry id exactly matches the tag value.
- Check the item tag was included in the built resources or jar.
- Check whether `ICurioItem.canEquip` or a predicate rejects the item.
- Check whether the slot has free capacity.

### Item equips but has no effect

- Check whether Java behavior is registered/exposed for that item.
- For attributes, check `ICurioItem#getAttributeModifiers(SlotContext, UUID, ItemStack)` is implemented or `CuriosApi.registerCurio(...)` is called during setup.
- Check that the modifier uses the `uuid` passed by Curios or another stable UUID, not a random UUID created every call.
- Check that the item is still assigned to the slot through `data/curios/tags/items/<slot>.json`.
- Check server/client side separation.
- Check whether the event or tick hook actually fires.
- Add a temporary targeted log around the hook, then remove it after verification.

### Item equips but does not render on player

- Check item inventory model first.
- Check Curios renderer registration.
- Check model/texture paths.
- Check whether the renderer is client-only and registered in the client setup path.
- If using GeckoLib, confirm a real GeckoLib renderer exists; asset files alone are not enough.

### Data works in dev but not packaged jar

- Check `processResources` output and final jar contents.
- Check Gradle source sets include `src/generated/resources` if data generation is used.
- Check `mods.toml` dependency ranges.
- Check case-sensitive paths: resource names should be lowercase.

## Version Notes

- For Forge 1.20.1, Curios 5.x is the common branch. Prefer the official 1.20.x documentation when method signatures matter.
- Newer docs may show APIs or NeoForge-era patterns that do not compile in older Forge projects.
- When uncertain, inspect the Curios jar sources in the Gradle cache or use IDE completion from the resolved dependency.

## Local Project Pattern Example

A custom "method" slot for a cultivation-themed mod can be wired like this:

```text
src/main/resources/data/curios_profundum/curios/slots/method.json
src/main/resources/data/curios_profundum/curios/entities/entities.json
src/main/resources/data/curios/tags/items/method.json
src/main/resources/assets/profundum_mod/textures/slot/method_slot.png
src/main/resources/assets/profundum_mod/lang/zh_cn.json
```

Example slot JSON:

```json
{
  "icon": "profundum_mod:slot/method_slot",
  "order": 20,
  "size": 1,
  "use_native_gui": true
}
```

Example entity JSON:

```json
{
  "entities": ["minecraft:player"],
  "slots": ["method"]
}
```

Example item tag:

```json
{
  "replace": false,
  "values": [
    "profundum_mod:enne_abysmal_pyro_alethia"
  ]
}
```

Example language entry:

```json
{
  "curios.identifier.method": "功法"
}
```

The chain to verify:

1. Slot definition declares `method`, size, order, and icon.
2. Entity data assigns `method` to `minecraft:player`.
3. Curios item tag lists every item allowed in `method`.
4. Optional Java item behavior implements version-correct Curios hooks.
5. Optional renderer registration handles body rendering.
