# Community 1.20.1 Guide

## 用途

本页提炼自中文社区教程《KubeJS教程-1.20.1》，用于补充官方文档里不够集中、但实战上非常常见的经验。

这页尤其适合在这些情况下阅读：

- 项目明确是 `1.20.1`
- 需要中文社区里常见的写法习惯
- 想知道哪些东西“能写”，但官方文档未必讲得很直白
- 想参考更多偏整合包作者视角的经验

## 这套中文教程补进来的核心价值

这份教程最大的价值，不是替代官方 wiki，而是把很多“做包时真的会遇到”的细节放在了一起。

它覆盖了这些方向：

- 文件夹分工与热重载经验
- 事件总览
- 配方、tag、战利品
- 资源路径
- ProbeJS
- `global`
- `scheduleInTicks`
- `Java.loadClass`
- `ForgeEvent`
- `RecipesSchema`
- 大量 addon 与项目案例

## 文件夹与重载的社区经验

教程对 `kubejs` 目录的介绍很实用：

- `assets` 放语言、贴图、模型等资源
- `data` 放数据包内容
- `client_scripts` 适合客户端逻辑，可配合 `F3 + T` 或 `kubejs reload client_scripts`
- `server_scripts` 放大多数逻辑处理，`reload` 会重载大部分服务端内容，但不一定涵盖配方
- `startup_scripts` 放注册类逻辑，例如物品、方块、流体；注册新内容通常仍应视为需要重启

它还有一个很重要的提醒：

- KubeJS 6 的内置资源包/数据包优先级较低，容易被其他资源包覆盖

这意味着：

- 如果只是做整合包内部快速魔改，`kubejs/assets` 与 `kubejs/data` 很方便
- 如果你要做“稳定发布、强约束不被覆盖”的资源内容，不能天真地假设 KubeJS 内置资源永远优先生效

## ProbeJS 是高优先级工具

这份教程非常强调 `ProbeJS`。
它把 ProbeJS 描述成“收集整合包中的方块/物品等信息，供 VS Code 文本编辑器使用”，并指出装上模组后运行对应命令，就能给 KubeJS 脚本提供补全与信息提示。

对 AI 来说，这意味着：

- 如果本地项目已经在用 ProbeJS 生成的补全环境，应优先信任本地补全结果
- 当用户问“怎么查注册名”“为什么没有提示”时，应该想到 ProbeJS，而不是只谈 KubeJS Core

## Tag 的社区理解方式

这份教程对 tag 的解释非常适合作为 AI 的教学口吻：

- tag 可以先粗略理解为“组”
- `/kjs hand` 不只会显示物品 id，还能看到带 `#` 的 tag id
- 常用 tag 类型是 `block`、`fluid`、`item`

并且直接给出了常用入口：

```js
// 这个示例用于在 server_scripts 中按类型修改标签，可根据方块、流体或物品分别处理。
ServerEvents.tags('block', event => {})
ServerEvents.tags('fluid', event => {})
ServerEvents.tags('item', event => {})
```

这很适合教 AI 在回答时先帮用户分清：

- 他要改的是 block tag、fluid tag，还是 item tag
- 问题是在“某个物品属于哪些组”，还是“我要把它塞进哪个组”

## 定时任务的实用说法

社区教程把定时任务说得很直白：如果你想让事件在几秒后执行，而不是立刻执行，就用 `server` 上的 `scheduleInTicks`。

示例思路是：

```js
// 这个示例用于在若干 tick 后执行一段逻辑，适合延迟提示、延迟结算或二段式交互。
Utils.server.scheduleInTicks(20 * 5, () => {
  Utils.server.tell('hello -- 定时任务触发了')
})
```

AI 在使用这类写法时应注意：

- 这是 tick 级延迟，不是现实时间 API
- `20` tick 约等于 1 秒
- 能从事件里直接拿 `server` 就优先拿事件中的 `server`

## global 的社区理解方式

社区教程把 `global` 讲成：

- 一个能在 `server`、`startup`、`client` 三端调用的全局变量容器

并给出了把函数、数字、字符串、数组、对象挂上去再跨脚本访问的思路。

这对 AI 很有用，因为很多用户会把 `global` 当成“跨文件共享配置”或“共享工具函数”的入口。

但结合官方 1.21 变化时要提醒：

- 在更新版本里，`global` 的限制变多了
- 因此必须先看本地版本，再决定是否照搬老教程

## Java.loadClass 的定位

这份中文教程态度非常鲜明：`Java.loadClass` 很强，但不该滥用。

它给出的判断标准很靠谱：

- 如果没有一定 Java 基础，不建议乱碰
- 绝大多数时候不需要它
- 真要用时，往往是为了解锁 KubeJS 默认没包好的 Java 类与第三方 mod API

AI 应据此遵守一个实践规则：

- 默认不要把 `Java.loadClass` 当作第一选择
- 只有在官方/项目明确已经在走这条路，或用户要求调用特定 Java 类时，再考虑它

## ForgeEvent 的位置与风险

这份教程特别提醒：

- `ForgeEvent` 要写在 `startup` 里
- 它不仅能抓 Forge 自带事件，也可能抓到其他模组自己定义、但继承了 `Event` 的事件
- 它常和 `loadClass` 搭配使用

并且教程建议：

- 去 CRT 文档和 Forge 源码里查对应事件类

这对 AI 的启发是：

- `ForgeEvent` 是高自由度、高风险区域
- 不要把它当成普通 KubeJS 事件的替代品
- 如果 Core 事件能解决，就不要先上 ForgeEvent

## RecipesSchema 是 1.20.1 的专项技巧

这份教程指出：

- `recipeSchemaRegistry` 是 1.20 时代的 `Startup` 事件能力
- 用途是给“还没有 KubeJS 适配”的配方类型补一层简单适配
- 到 `1.21` 这一套做法就已经变化了

因此 AI 应这样理解：

- `RecipesSchema` 很适合 1.20.1 的整合包作者给冷门模组补配方桥接
- 但绝对不能不看版本就往 1.21+ 项目里硬搬

## 社区教程还覆盖了哪些方向

从目录看，这份教程还系统覆盖了：

- 物品提示
- 战利品分类型处理
- 自定义物品、方块、流体、工具材料、盔甲材料、附魔、药水
- 静态类工具集
- 自定义指令
- 世界生成
- addon 模组页，如 `CreateJS`、`LootJS`、`JEI&REIJS`、`ThermalJS`、`PonderJS`
- 项目分享页，如自定义传送门、服务器签到、聊天增强、配方导出等

所以当用户问的问题明显很“偏项目经验”时，这份中文教程常常会比官方 wiki 更贴近整合包作者的真实使用场景。

## 这页与官方文档如何配合

最好的用法不是二选一，而是这样分工：

- 用官方文档确认“标准入口与正式 API 名称”
- 用这份中文教程补足“版本经验、项目感、社区常用技巧”

如果两边冲突：

1. 先看本地项目实际写法
2. 再看 Minecraft/KubeJS 版本
3. 最后优先信任与本地版本匹配的官方页面

## 来源

- 中文教程首页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1)
- 文件夹说明页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsbasic/filestructure](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsbasic/filestructure)
- 事件总览页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsbasic/allevent](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsbasic/allevent)
- ProbeJS 基础页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsbasic/probejs](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsbasic/probejs)
- Tag 页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsbasic/tags](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsbasic/tags)
- 静态类页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/staticclsss](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/staticclsss)
- 自定义指令页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/customcommandregistry](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/customcommandregistry)
- 定时任务页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/scheduleinticks](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/scheduleinticks)
- global 页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/globalvariable](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/globalvariable)
- LoadClass 页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/javaloadclass](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/javaloadclass)
- ForgeEvent 页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/forgeevent](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/forgeevent)
- RecipesSchema 页：
  [https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/recipesschemaadded](https://gumeng.gitbook.io/kubejs-jiao-cheng-1.20.1/kubejsadvanced/recipesschemaadded)
