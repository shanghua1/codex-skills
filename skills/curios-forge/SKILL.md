---
name: curios-forge
description: Explain, implement, and debug Curios API integrations in Minecraft Forge mods, especially slots, entity slot assignment, item slot tags, ICurioItem behavior, curio capabilities, attribute modifiers such as attack damage or max health bonuses, renderer setup, inventory access, dependencies, data generation, and common missing-slot issues. Use when Codex is working on a Forge mod that depends on Curios or when the user asks how to add wearable items, rings, amulets, charms, custom curios slots, Curios-compatible equipment, or stat bonuses while equipped.
---

# Curios Forge Modding

Use this skill when the task is about Curios-specific Forge modding rather than generic Minecraft items.
Curios is mostly a contract between Java registration, data-pack JSON, item tags, entity slot binding, optional item capabilities, optional client renderers, and player inventory APIs.

## First Pass Workflow

1. Identify the Minecraft version, Forge version, Curios version, and whether the project uses ForgeGradle resource processing.
2. Inspect `build.gradle`, `gradle.properties`, `src/main/java`, and `src/main/resources/data` before editing.
3. Find the mod id and existing Curios namespaces. Check for both `data/<your_mod>/curios/slots/*.json` and `data/curios/tags/items/*.json`.
4. Decide whether the request is only data-driven slot/item assignment or needs Java behavior via `ICurioItem`, capabilities, attribute modifiers, renderers, or inventory access.
5. Preserve local patterns. If the project already uses data generation, add providers there instead of hand-writing JSON unless the repo is resource-first.
6. After editing, validate by checking JSON parseability, resource paths, namespace consistency, and game logs for Curios messages.

## Read The Reference When Needed

Read [references/curios-forge-reference.md](./references/curios-forge-reference.md) when the task requires any of these details:

- adding or renaming a Curios slot
- assigning items to slots
- making a custom `ICurioItem`
- adding Curios attribute bonuses such as attack damage, max health, armor, speed, or extra slot counts
- reading, equipping, or unequipping items from code
- adding custom renderer behavior
- debugging slots not appearing, items not equipping, or data not loading
- checking version differences between 1.20.x and newer Curios docs

## Core Mental Model

- Slot type definition: usually `src/main/resources/data/<modid>/curios/slots/<slot>.json`.
- Entity slot assignment: usually `src/main/resources/data/<modid>/curios/entities/*.json`.
- Item eligibility: usually `src/main/resources/data/curios/tags/items/<slot>.json`.
- Item behavior: Java item implements or provides `ICurioItem` when it needs ticking, equip checks, attributes, drops, renderer state, or special behavior.
- Curios attributes: fixed item bonuses can come from `ICurioItem#getAttributeModifiers`; NBT or generated loot bonuses can use `CurioAttributeModifiers`.
- Inventory access: use Curios API helpers rather than scanning vanilla armor/offhand inventories.
- Client rendering: requires renderer registration; a `.geo.json` or item model file alone does not make a Curios body renderer happen.

## Common Failure Checks

When something "does not show" or "cannot equip", check in this order. Do not flag a project as wrong merely because it uses a separate data namespace such as `curios_profundum`; that is valid if the files are in the Curios data folders and the game logs show Curios loading them.

1. The Curios dependency is declared in `mods.toml` and Gradle, and the runtime actually loads Curios.
2. The slot JSON exists under the correct data namespace and parses.
3. The slot is assigned to the target entity type, usually players, through Curios entity data or the expected preset.
4. The item is in `data/curios/tags/items/<slot>.json`, not only in the mod's own namespace.
5. The item id in the tag matches the registered item id exactly.
6. The slot size is greater than 0 and the slot is not hidden by config or GUI settings.
7. If behavior is Java-driven, the item actually exposes Curios behavior through the correct API for the project version.
8. If rendering is expected, the client renderer is registered and the model/texture paths are valid.

## Editing Rules

- Use official Curios docs as the primary source when browsing is available; use community tutorials only to supplement patterns and pitfalls.
- Do not invent APIs across versions. Curios APIs changed over time; confirm against the version in `gradle.properties` or dependencies.
- Keep slot ids lowercase and stable. Renaming a slot is a data migration for worlds and configs.
- Prefer data-only integration for simple "this item fits this slot" work.
- Use Java `ICurioItem` only when the item needs lifecycle hooks, predicates, attributes, ticking, renderer behavior, or drop rules. For fixed stat bonuses, implement or register `ICurioItem#getAttributeModifiers(SlotContext, UUID, ItemStack)`.
- Use generated resources when the repo already has data generation; otherwise hand-written JSON is acceptable and easier to inspect.

## Output Style

When answering or implementing:

- State which files changed and why.
- Mention whether the user needs game restart, data reload, or resource reload.
- Include a compact "why Curios will see this" chain: slot definition -> entity binding -> item tag -> optional Java behavior.
- For debugging, quote the relevant log line or JSON path rather than giving generic advice.
