---
name: kubejs
description: Explain and modify Minecraft KubeJS projects, including what KubeJS is, how `startup_scripts`, `server_scripts`, `client_scripts`, `data`, and `assets` are used, and how to write or update event listeners, recipes, tags, loot logic, item registration, and other modpack customization scripts. Use when Codex needs KubeJS-specific guidance rather than generic JavaScript advice.
---

# KubeJS Modding

## Overview

Use this skill when the user wants AI to understand KubeJS itself, not just to edit random JavaScript.
Treat KubeJS as a Minecraft modpack customization layer with version-specific APIs, folder rules, reload behavior, and many event-driven patterns.

## What KubeJS Is

KubeJS lets modpack authors customize Minecraft with JavaScript and data-pack style resources.
Typical uses include:

- modifying vanilla or modded recipes
- adding or removing tags
- registering custom items, blocks, and fluids
- changing tooltips or client presentation
- reacting to item, block, chat, or entity events
- adjusting progression, loot, and pack balance

Prefer KubeJS-native patterns over generic JavaScript abstractions. The important question is usually not only "how do I write the code?" but also "which KubeJS folder and event does this belong to?"

## First Pass Workflow

When this skill triggers:

1. Identify the Minecraft version, mod loader, and any KubeJS addon mods in play.
2. Inspect the local `kubejs/` tree before editing anything.
3. Detect whether the project uses modern event syntax such as `ServerEvents.recipes(...)` or older legacy syntax such as `onEvent(...)`.
4. Preserve the local style if the project already has one. If the project is blank, prefer the modern event APIs shown in current official docs.
5. Verify item ids, block ids, tags, recipe ids, and addon-specific namespaces from project files or official docs before making changes.

## Which Reference To Read

Read only the references needed for the current task:

- For an overview of the official KubeJS wiki structure and where to look first, read [references/wiki-map.md](./references/wiki-map.md).
- For folder meanings, reload behavior, and "where should this code live?", read [references/folder-structure.md](./references/folder-structure.md).
- For event families, listener syntax, and common script patterns, read [references/events-and-patterns.md](./references/events-and-patterns.md).
- For recipe, tag, and progression-related edits, read [references/recipes-and-tags.md](./references/recipes-and-tags.md).
- For item, block, and fluid registration plus modification-time builders, read [references/registration-and-builders.md](./references/registration-and-builders.md).
- For tooltips, item/block interactions, chat hooks, and command registration, read [references/interactions-chat-and-client.md](./references/interactions-chat-and-client.md).
- For global utilities, class docs, and version-specific caveats, read [references/global-scope-and-version-notes.md](./references/global-scope-and-version-notes.md).
- For addon discovery and mod-specific integrations, read [references/addons-and-integrations.md](./references/addons-and-integrations.md).
- For community-tested KubeJS 1.20.1 practices from the Chinese GitBook tutorial, read [references/community-1-20-1-guide.md](./references/community-1-20-1-guide.md).
- For official source links and when to look them up, read [references/source-map.md](./references/source-map.md).

## Core Rules

- Do not invent KubeJS APIs from memory if the local project or official wiki can confirm them.
- Prefer small, focused script files named after the feature they affect.
- Explain gameplay impact, not only code mechanics.
- When showing example code, start the code block with a Chinese comment line that states the purpose of the snippet.
- If the user asks for a specific mod integration such as Create, Mekanism, or LootJS, inspect the installed mods and then consult the matching addon docs before writing code.

## Common Requests This Skill Should Handle

- "How do I listen for a right-click block event in KubeJS?"
- "Which folder should this recipe script live in?"
- "Remove the vanilla iron pickaxe recipe from this modpack."
- "How do I add tooltip text to an item?"
- "What is the difference between `startup_scripts` and `server_scripts`?"
- "This project still uses `onEvent`; should we migrate it to the newer style?"

## Output Style

When answering with code:

- mention which folder the snippet belongs in
- explain whether it needs restart, `/reload`, or client resource reload
- point out version caveats if the code may differ between modern and legacy KubeJS styles
- keep examples practical and pack-author-oriented
