# Events And Patterns

## Purpose

Read this file when the task is about listening to events, modifying items or blocks, adding tooltips, or deciding between modern and legacy KubeJS syntax.

## Modern Vs Legacy Style

Current official docs prefer named event families such as `ServerEvents`, `StartupEvents`, `ItemEvents`, and `BlockEvents`.
Older packs may still use legacy `onEvent('...')` syntax.

Rules:

- If the local project is already using legacy style consistently, preserve it unless the user asks for migration.
- If the project is new or mixed, prefer the modern event family style.
- Do not mechanically rewrite a whole pack to modern syntax as part of an unrelated task.

## Common Event Families

- `StartupEvents`
  For registration-time logic such as creating items, blocks, and fluids.
- `ServerEvents`
  For recipes, tags, and many server-side gameplay edits.
- `ItemEvents`
  For item interactions, tooltip-related hooks, and item modification flows.
- `BlockEvents`
  For block interactions such as breaking or right-clicking.
- `ClientEvents` or client-side event families
  For client-only logic where supported by the installed KubeJS version and addons.

## Example: Register A Custom Item

```js
// 这个示例用于在 startup_scripts 中注册一个自定义物品，通常需要重启游戏后才能稳定生效。
StartupEvents.registry('item', event => {
  event.create('example_relic').displayName('Example Relic')
})
```

Use this pattern in `kubejs/startup_scripts/`.

## Example: Add A Tooltip

```js
// 这个示例用于在 client_scripts 中给指定物品添加提示文本，影响客户端显示而不直接改变配方逻辑。
ItemEvents.tooltip(event => {
  event.add('minecraft:diamond', '这是一颗被整合包重点关注的钻石。')
})
```

Use this pattern in `kubejs/client_scripts/`.

## Example: React To A Broken Block

```js
// 这个示例用于监听方块被破坏后的行为，并在满足条件时取消默认结果或替换方块。
BlockEvents.broken('minecraft:stone', event => {
  event.block.set('minecraft:cobblestone')
  event.cancel()
})
```

Use this pattern in `kubejs/server_scripts/` unless the local pack clearly keeps similar handlers elsewhere.

## Example: Modify Existing Items

```js
// 这个示例用于在启动阶段调整已有物品属性，例如堆叠上限、抗火性或稀有度。
ItemEvents.modification(event => {
  event.modify('minecraft:ender_pearl', item => {
    item.maxStackSize = 64
    item.fireResistant = true
  })
})
```

This kind of change belongs in `kubejs/startup_scripts/`.

## Decision Rule

If the user asks "怎么监听", first determine:

1. Is the behavior startup-only, server-side, or client-side?
2. Is there already an event family used in the project?
3. Does the task require restart or only `/reload`?

Then choose the smallest event hook that matches.
