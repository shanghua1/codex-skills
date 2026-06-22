# Global Scope And Version Notes

## 用途

当任务需要跨脚本共享数据、使用全局工具类、查包装类，或判断某种写法是不是版本差异造成的，读这一页。

## Global Scope 有什么

官方 `Global Scope` 页面列出了一批在脚本里随处可用的对象与类。

常见项包括：

- `global`
- `console`
- `SECOND`
- `MINUTE`
- `HOUR`
- `Platform`
- `ResourceLocation`
- `Utils`
- `Java`
- `Text`
- `Item`
- `Block`
- `Ingredient`
- `NBT`

官方还列出了字符串辅助：

- `string.namespace`
- `string.path`

也就是对 `minecraft:oak_planks` 这种 id，可以方便拆 namespace 和 path。

## global 对象怎么用

官方 `Global Object` 页面给出的核心用途，是在不同脚本类型间共享数据。

```js
// 这个示例用于在 startup_scripts 中记录一批项目共用定义，并在 server_scripts 里继续复用。
global.items = [
  ['hi_wiki', '4x diamond']
]
```

```js
// 这个示例用于在 startup_scripts 中批量注册 global 中定义的物品。
StartupEvents.registry('item', event => {
  global.items.forEach(items => event.register(items[0]))
})
```

```js
// 这个示例用于在 server_scripts 中复用同一份 global 数据，批量补配方。
ServerEvents.recipes(event => {
  global.items.forEach(items => event.shapeless(items[0], items[1]))
})
```

## 版本差异提醒

官方 `7.0 (1.21)` 页面给了几条非常值得记住的迁移提醒：

- 未来一段时间只支持 NeoForge
- 新 wiki 主要反映 1.21+ 信息，旧版本要看 legacy 内容
- 高级战利品事件在迁往 LootJS
- 世界生成事件当前被弱化或停用，后续可能转到 addon
- `global` 现在限制为只能从 `startup script` 里设置值
- 原生事件与配方系统在 1.21 代有进一步变化

这意味着：

- 如果项目是 1.21+，不要轻易照搬 1.19.2 老帖子的写法
- 如果项目是 1.20.1 或以下，看到新 wiki 某些行为不一致，并不一定是你记错，可能就是版本不同

## 1.19.2 时代的重要变化

官方 `6.1 (1.19.2)` 页面提到 recipe schema 等重大变化。
实战上可以理解为：

- 1.19.2 是现代 KubeJS 生态的重要分水岭
- 很多旧教程与旧社区片段和现代文档并不完全兼容
- 如果本地项目明显沿用老风格，优先跟本地，再决定要不要迁移

## Reference 应该怎么用

官方 `Reference` 页面更像类文档目录，不是入门教程。
当你已经知道要查什么类时，再去那里最有效。

常见目标类包括：

- `BlockBuilder`
- `MutableItem`
- `MutableBlock`
- `MutableToolTier`
- `FluidAmounts`
- `SoundType`
- `Color`
- `ID`

## 实战规则

- 需要“先会做”时，看教程页
- 需要“知道放哪里”时，看目录页
- 需要“深挖属性和类”时，看 Reference
- 需要“怀疑是版本差异”时，看 Major Updates
