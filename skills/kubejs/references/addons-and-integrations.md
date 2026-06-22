# Addons And Integrations

## 用途

当任务涉及某个具体模组或插件时，读这一页。

## 为什么这页重要

很多 KubeJS 写法并不属于 KubeJS Core，而是某个 addon 提供的。
如果不先确认 addon，AI 很容易凭想象捏出不存在的方法名。

## 官方 Addons 区块怎么用

官方 wiki 有一个 `Addons` 总索引页，列出大量集成模组页面。

实战中应先做两步：

1. 从本地整合包或 `mods/` 目录确认相关模组与 addon 是否真的安装
2. 再去官方 `Addons` 区块打开对应页面

## 常见判断方式

如果用户提到这些词，通常就不该只看 KubeJS Core：

- `Create`
- `Mekanism`
- `LootJS`
- `EMI`
- `JEI`
- `Thermal`
- `Tinkers`

## Create 是典型例子

官方 `Create` addon 页面专门讲 Create 相关 recipe 写法。
如果用户说“帮我改黄铜混合”“帮我加粉碎轮配方”“帮我改机械手工序列组装”，就应该优先去读 Create addon 页，而不是凭通用 `ServerEvents.recipes(...)` 瞎写。

## 关于 LootJS

官方 1.21 大版本说明提到，高级战利品事件在向 LootJS 迁移。
这意味着：

- 旧版本里某些战利品做法可能还写在 KubeJS 体系内
- 新版本里更复杂的 loot 改动，可能该去查 LootJS 文档

## 实战规则

- 用户说的是“某模组特有机器配方”时，先确认 addon 页面
- 用户说的是“通用配方删改”时，先看 `recipes-and-tags.md`
- 用户说的是“显示层集成”时，留意 EMI/JEI 类页面
- 任务一旦出现陌生方法名，优先怀疑“这是不是 addon API”
