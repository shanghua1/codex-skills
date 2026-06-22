# Interactions, Chat, And Client

## 用途

当任务涉及提示文本、右键行为、破坏行为、聊天监听、自定义命令、JEI/EMI 或客户端表现时，读这一页。

## Tooltip 属于客户端逻辑

官方 `Item Tooltips` 页面把这类逻辑放在 `client_scripts/` 范畴内。

```js
// 这个示例用于在 client_scripts 中给多个物品添加提示文本，只影响客户端显示。
ItemEvents.tooltip(event => {
  event.add(['quark:backpack', 'quark:magnet', 'quark:crate'], 'Added by Quark Oddities')
  event.add(/refinedstorage:red_/, 'Can be any color')
})
```

官方还特别提醒：

- 这里可以传物品 id 数组
- 可以传正则
- 不适合直接依赖 `#tag`，因为标签在客户端脚本阶段加载得更晚

## 方块交互

官方 `Block Interactions` 页面说明：这些事件可以放在 `server_scripts/` 或 `client_scripts/`，要看你想改的是服务端行为还是客户端表现。

破坏事件示例：

```js
// 这个示例用于监听特定方块被破坏后的行为，并把石头替换成圆石。
BlockEvents.broken('minecraft:stone', event => {
  event.block.set('minecraft:cobblestone')
  event.cancel()
})
```

从官方页可知：

- `BlockEvents.broken(...)` 是可取消的
- 页面还列了 `right clicked`、`left clicked`、`placed`、`farmland trampled` 等交互类目
- 如果任务是世界状态改动，通常更偏 `server_scripts/`

## 物品交互

官方 `Item Interactions` 页面展示了实体右键交互等模式。

```js
// 这个示例用于让玩家拿桶右键山羊时获得奶桶，属于典型的物品交互事件。
ItemEvents.entityInteracted('minecraft:bucket', event => {
  if (event.target.type != 'minecraft:goat') return
  event.item.count--
  event.player.giveInHand('minecraft:milk_bucket')
  event.target.playSound('minecraft:entity.cow.milk')
})
```

这类写法适合：

- 右键实体
- 右键空气或使用物品
- 触发某些特殊道具效果

## 聊天事件

官方 `Chat Events and Commands` 页面把聊天监听放在 `PlayerEvents.chat(...)`。

```js
// 这个示例用于监听聊天内容，并在玩家说出指定词时延迟向全服回复一条消息。
PlayerEvents.chat(event => {
  if (event.message.trim().toLowerCase() == 'creeper') {
    event.server.scheduleInTicks(1, event.server, ctx => {
      ctx.data.tell(Text.green('Aw man'))
    })
  }
})
```

同页还给了“拦截聊天并取消”的模式：

```js
// 这个示例用于把以特定前缀开头的聊天消息当作简单命令处理，并阻止原消息继续发送。
PlayerEvents.chat(event => {
  if (event.message.startsWith('!some_command')) {
    event.player.tell('Hi!')
    event.cancel()
  }
})
```

## 修饰聊天内容

官方还提供 `PlayerEvents.decorateChat(...)` 来改消息内容，而不是取消消息。

```js
// 这个示例用于在聊天消息发出前替换其显示文本，适合做格式化与轻量玩梗效果。
PlayerEvents.decorateChat(event => {
  event.setMessage(event.message.replace(':sword:', '⚔'))
})
```

注意：

- 这个事件在官方文档中标注为 1.19.2+ 才有
- 它不用于取消消息，而是用于修改显示内容

## 注册命令

官方文档给出的标准入口是 `ServerEvents.commandRegistry(...)`。

```js
// 这个示例用于注册一个简单的服务端命令，适合整合包管理、调试或玩法开关。
ServerEvents.commandRegistry(event => {
  const { commands: Commands, arguments: Arguments } = event
  event.register(
    Commands.literal('fly')
      .requires(source => source.hasPermission(2))
      .executes(ctx => {
        const player = ctx.source.player
        player.abilities.mayfly = !player.abilities.mayfly
        return 1
      })
  )
})
```

如果任务只是做轻量聊天口令，未必需要注册正式命令；但如果要权限、补全、参数解析，优先走 `commandRegistry`。

## 客户端逻辑的判断法

优先放 `client_scripts/` 的情况：

- tooltip
- JEI/REI/EMI 展示层处理
- 纯显示性的文本与视觉效果
- 资源重载后应立即更新的东西

优先放 `server_scripts/` 的情况：

- 会改变世界状态
- 会改配方、掉落、实体、方块行为
- 需要服务端权威执行

## 关于热重载

按官方文档：

- `client_scripts/` 常配合 `F3 + T` 或 `/kubejs reload client_scripts`
- `server_scripts/` 常配合 `/reload`

因此写示例时，最好顺手告诉用户“改完是重载资源、重载数据，还是必须重启”。
