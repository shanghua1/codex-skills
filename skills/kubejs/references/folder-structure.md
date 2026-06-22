# Folder Structure

## Purpose

Read this file when you need to decide where a KubeJS change belongs, what kind of reload it needs, or whether a script should be treated as startup, server, client, data, or asset content.

## The Main KubeJS Folders

- `kubejs/startup_scripts/`
  Used for things that are effectively part of game startup or registration time, such as custom item, block, or fluid registration and certain modifications that require a restart.
- `kubejs/server_scripts/`
  Used for recipes, tags, loot edits, progression logic, and many server-side gameplay events. This is the most common place for modpack balance edits.
- `kubejs/client_scripts/`
  Used for client-only behavior such as tooltips, JEI or EMI integration, and visual or interface-related logic.
- `kubejs/data/`
  Used for data-pack style JSON resources. Prefer this when the change is better expressed as structured JSON rather than JavaScript.
- `kubejs/assets/`
  Used for resource-pack style files such as lang entries, models, textures, and client-facing assets.

## Reload Heuristics

- `startup_scripts/`: usually requires a full game restart for registered content; some top-level code can be reloaded with `/kubejs reload startup_scripts`, but registration changes should be treated as restart-sensitive.
- `server_scripts/`: usually reloads with `/reload`; use this for recipes and many gameplay rules.
- `client_scripts/`: usually reloads with client resource reload, commonly `F3 + T`, and some top-level code can use `/kubejs reload client_scripts`.

## How To Choose A Folder

- If the task adds or changes recipes, tags, loot, or progression logic, start with `server_scripts/`.
- If the task registers new items, blocks, or fluids, start with `startup_scripts/`.
- If the task changes tooltip text, recipe viewer integration, or other client presentation, start with `client_scripts/`.
- If the task is mostly JSON-driven, check whether `kubejs/data/` or `kubejs/assets/` is cleaner than a JavaScript event.

## Practical Rule

If the project already has a local convention, follow the project first and this file second.
