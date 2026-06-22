# Registration And Builders

## 用途

当任务涉及“新增内容”或“改已有内容属性”时，读这一页。

## 这类任务通常属于哪一层

官方文档把这些事情大多放在 `startup_scripts/`：

- 注册新物品
- 注册新方块
- 注册新流体
- 修改已有物品属性
- 修改已有方块属性

因为这些逻辑通常与注册阶段绑定，很多改动不能像配方那样只靠 `/reload` 即时生效。

## 注册物品

官方 `Item Registry` 页面说明：自定义物品在启动脚本中创建，不能只靠热重载稳定生效，通常应视为需要重启。

```js
// 这个示例用于在 startup_scripts 中注册一个新的物品，适合做任务物品、流程道具或自定义材料。
StartupEvents.registry('item', event => {
  event.create('test_item')
    .displayName('测试物品')
})
```

补充要点：

- 贴图通常要放进 `kubejs/assets/kubejs/textures/item/`
- 自定义模型一般放进 `kubejs/assets/kubejs/models/item/`
- `event.create()` 返回 builder，可继续链式调用属性

## 注册方块

官方 `Block Registry` 页面展示了 `event.create(...).displayName(...).hardness(...)` 这一类链式 builder 写法。

```js
// 这个示例用于在 startup_scripts 中注册一个自定义方块，并顺手设置若干常见属性与挖掘标签。
StartupEvents.registry('block', event => {
  event.create('example_block')
    .displayName('示例方块')
    .soundType('wool')
    .hardness(1)
    .resistance(1)
    .requiresTool(true)
    .tagBlock('minecraft:mineable/pickaxe')
    .tagBlock('minecraft:needs_iron_tool')
})
```

这类 builder 风格在 KubeJS 里非常常见：

- 先 `event.create(id)`
- 再链式追加属性
- 最后由 KubeJS 帮你生成默认资源或读取你自定义的资源文件

## 注册流体

官方 `Fluid Registry` 页面说明：流体也在启动脚本注册，并且 1.21 时代的写法比旧版更友好，支持 `'thin'` 与 `'thick'` 两类预设。

```js
// 这个示例用于在 startup_scripts 中注册一个流体，适合整合包新增材料液、工艺液或主题液体。
StartupEvents.registry('fluid', event => {
  event.create('water_clone', 'thin')
    .displayName('Water Clone')
    .tint(0x3F76E4)
})
```

官方文档还强调了几件事：

- `stillTexture(...)`、`flowingTexture(...)`、`renderType(...)` 是核心概念
- `'thin'` 与 `'thick'` 只是帮你预填了一部分默认行为
- 想要真正好看的视觉效果，往往还得配自己的贴图

## 修改已有物品

官方 `Item Modification` 页面给出的标准写法是 `ItemEvents.modification(...)`。

```js
// 这个示例用于在启动相关阶段修改已有物品属性，例如堆叠数、稀有度、燃料时长或返还物。
ItemEvents.modification(event => {
  event.modify('minecraft:ender_pearl', item => {
    item.maxStackSize = 64
    item.fireResistant = true
    item.rarity = 'UNCOMMON'
  })
})
```

官方还指出：

- `MutableItem` 还有更完整的属性表，应在官方 `Reference` 里查
- 这类任务适合“调现有物品行为”，不适合“伪装注册新物品”

## 修改已有方块

如果任务是“让某类方块更硬、更耐爆、需要某工具等级”，优先去找官方 `Block Modification` 与 `Reference/MutableBlock`。
如果只是要影响挖掘标签，也可以直接通过方块注册或标签逻辑完成。

## Builder 风格的理解方式

很多 KubeJS 文档都在使用 builder 模式。判断方法很简单：

- `event.create(...)` 返回一个可链式配置的 builder
- `event.modify(...)` 里给你的对象通常是可直接赋值的可变对象
- 想知道某个 builder 或 mutable 对象到底有哪些方法/字段，去官方 `Reference` 查具体类名

## 什么时候去查 Reference

遇到这些情况时，不要硬猜：

- 你知道有个属性能改，但不记得名字
- 你知道要改 builder，但不清楚是否支持链式方法
- 你看到官方教程只给了简单示例，想深挖全部字段

这时去官方 `Reference` 看：

- `BlockBuilder`
- `MutableItem`
- `MutableBlock`
- `FluidAmounts`
- `SoundType`
- `Color`
