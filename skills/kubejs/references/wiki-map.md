# Wiki Map

## 用途

当任务还很模糊，只知道“要改 KubeJS”，但还不清楚该先看哪类资料时，先读这一页。

## 官方 KubeJS Wiki 的大区块

官方文档大体可以分成这些区域：

- `Tutorials`
  最适合找“怎么写”的页面。大多数整合包作者日常要用的写法都在这里。
- `Folder Structure`
  最适合判断代码应该放在哪个目录、能不能热重载、需不需要重启。
- `Events`
  最适合查有哪些事件家族、事件类型和事件类。
- `Addons`
  最适合查 Create、Mekanism、LootJS、JEI/EMI 等集成页面。
- `Global Scope`
  最适合查全局可用对象、工具类、常量和包装类。
- `Reference`
  最适合在你已经知道类名后，进一步查 `MutableItem`、`BlockBuilder`、`FluidAmounts`、`SoundType` 之类的细节。
- `Other / Major Updates`
  最适合查大版本迁移注意事项，例如 1.19.2 或 1.21 的语法与行为变化。

## 常见需求应该先看哪里

- “这个脚本放哪里？”
  先看 `folder-structure.md`
- “这个事件怎么监听？”
  先看 `events-and-patterns.md`，再看官方 `Events`
- “这个配方怎么删、怎么加、怎么替换材料？”
  先看 `recipes-and-tags.md`
- “我想注册一个物品、方块、流体”
  先看 `registration-and-builders.md`
- “我想改 tooltip、右键行为、聊天命令”
  先看 `interactions-chat-and-client.md`
- “这个类还有哪些属性能改？”
  先看 `global-scope-and-version-notes.md`，再跳转官方 `Reference`
- “这是某个模组特有写法吗？”
  先看 `addons-and-integrations.md`

## 学习顺序建议

如果是第一次接触 KubeJS，推荐按这个顺序理解：

1. `folder-structure.md`
2. `events-and-patterns.md`
3. `recipes-and-tags.md`
4. `registration-and-builders.md`
5. `interactions-chat-and-client.md`
6. `global-scope-and-version-notes.md`

## 实战心法

KubeJS 不只是“写 JS”。
更重要的是先判断：

- 这是启动期逻辑还是运行期逻辑
- 这是服务端行为还是客户端表现
- 这是 KubeJS Core 的能力还是某个 addon 的能力
- 这个项目是现代写法还是老写法

想清这四件事，通常就知道该从哪页下手了。
