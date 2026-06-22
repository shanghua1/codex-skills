# Recipes And Tags

## Purpose

Read this file when the task is about crafting, machine processing, item or block tags, progression gates, or recipe cleanup.

## Recipe Work Usually Lives In Server Scripts

The official KubeJS docs place recipe editing in `kubejs/server_scripts/`.
This is the default location for:

- adding shaped or shapeless recipes
- removing unwanted vanilla or modded recipes
- replacing outputs or ingredients
- creating machine recipes through addon integrations
- adjusting progression pacing

## Example: Add A Shaped Recipe

```js
// 这个示例用于在 server_scripts 中新增一个合成表配方，适合做前期流程或配方重构。
ServerEvents.recipes(event => {
  event.shaped('minecraft:hopper', [
    'I I',
    'ICI',
    ' I '
  ], {
    I: 'minecraft:iron_ingot',
    C: 'minecraft:chest'
  })
})
```

## Example: Remove A Recipe

```js
// 这个示例用于删除指定输出物的配方，常用于禁用原版捷径或为整合包让路。
ServerEvents.recipes(event => {
  event.remove({ output: 'minecraft:golden_apple' })
})
```

## Example: Replace An Ingredient

```js
// 这个示例用于批量替换配方中的输入材料，适合统一推进某一阶段的配方门槛。
ServerEvents.recipes(event => {
  event.replaceInput({}, 'minecraft:iron_ingot', 'minecraft:copper_ingot')
})
```

## Tag Editing

Tags are also typically edited in `kubejs/server_scripts/`.
Use tags when the user wants to affect a group of items or blocks rather than one exact id.

## Example: Add Items To A Tag

```js
// 这个示例用于把物品加入某个标签，方便让多份配方或检测逻辑共用同一批物品集合。
ServerEvents.tags('item', event => {
  event.add('forge:ingots/copper', 'minecraft:copper_ingot')
})
```

## Practical Rules

- Prefer tag-based balancing over copy-pasting many single-item conditions when the pack already uses tags heavily.
- If the change affects one exact recipe, target that recipe narrowly.
- If the change affects a category of items, consider tags first.
- For addon machine recipes, inspect the addon's own KubeJS integration docs before inventing method names.
