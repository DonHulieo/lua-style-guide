<!-- markdownlint-configure-file { "no-inline-html": { "allowed_elements": ["details", "summary"] } } -->
# FiveM Lua Style Guide

The style documentation for all `don` resources, helping maintain consistent & readable codebases.

This document is a work in progress and is subject to change. If you have any suggestions, please feel free to open an issue or a pull request.

## Table of Contents

- [FiveM Lua Style Guide](#fivem-lua-style-guide)
  - [Table of Contents](#table-of-contents)
  - [Credits](#credits)
  - [Formatting](#formatting)
    - [Indentation](#indentation)
    - [Whitespace](#whitespace)
    - [Comments](#comments)
  - [Naming](#naming)
    - [Constants](#constants)
    - [Variables](#variables)
    - [Objects](#objects)
    - [Functions](#functions)
    - [Classes](#classes)
    - [Events](#events)
    - [Files](#files)
    - [Git](#git)
      - [Commits](#commits)
      - [Versioning](#versioning)

## Credits

- [FiveM Docs](https://docs.fivem.net/docs/scripting-reference/)
- [Lua Reference Manual](https://www.lua.org/manual/)
- [LuaStyleGuide](http://lua-users.org/wiki/LuaStyleGuide)
- [Effective FiveM Lua](https://manason.github.io/effective-fivem-lua/)
- [FiveM Style Guide for Lua](https://github.com/project-error/fivem-lua-style)
- [Lua Language Server](https://luals.github.io/wiki/annotations/)

## Formatting

### Indentation

There is much debate over two or four spaces for indentation, while four spaces is the most common (amongst FiveM developers), two spaces is the most common amongst Lua developers.
> The [Programming in Lua](https://www.lua.org/pil/contents.html) book, the [Lua Reference Manual](https://www.lua.org/manual/), the [Beginning Lua Programming](https://www.amazon.com.au/dp/0470069171?ref_=mr_referred_us_au_au) book, the [lua-users-wiki](http://lua-users.org/wiki/) and the [Project Error](https://github.com/project-error/fivem-lua-style) use two spaces for indentation. As such, to follow the lua standard, we will use **two** spaces for indentation.

<details>
<summary>Example</summary>

```lua
for _, v in pairs(table) do
  print(v)
end
```

</details>

### Whitespace

Whitespace is instrumental in making code readable and should be used to separate elements.
> Following English grammar, `,`'s and all [operators](https://www.lua.org/manual/5.4/manual.html#3.4) (except `..`) should have a space after them at least. However, both parentheses and brackets should not have a space before or after them.

<details>
<summary>Example</summary>

```lua
local table = {1, 2, 3}
local string = 'hello'..'world' or 'hello' .. 'world'
local number = 1 + 2 * 3 / 4 - 5
local function hello_world() return string end
```

</details>

### Comments

Well written code should be self-documenting however, there will be times where additional comments are needed.
> Single line comments should have a space after the `--` and multiline comments should have `--` after the closing `]]`.
>
> Comments should endeavour to explain *why* the code is performing a function, opposed to *what* function it is performing.
>
> Each region of code should be separated by a:
>
> ```lua
> -------------------------------- [SUBREGION? REGION] --------------------------------
> ```
>
> Those regions follow the following format, with optonal subregions:
> | Region | Sub Region |
> | --- | --- |
> | `FUNCTIONS` | `LOCAL` \| `GLOBAL` \| `CLASS` |
> | `EVENTS` | `HANDLER` \| `NETWORK` |
> | `CALLBACKS` | |
> | `THREADS` | |
>
> Annotations should be used where reasonable and follow the [Lua Language Server](https://luals.github.io/wiki/annotations/) annotations.

<details>
<summary>Example</summary>

```lua
-------------------------------- LOCAL FUNCTIONS --------------------------------

---@param ped number The teleporting ped.
---@param coords {x: number, y: number, z: number}|vector3 The coordinates to teleport the ped to.
---@return boolean `true` if the player was teleported successfully.
local function teleport_ped(ped, coords)
  SetEntityCoords(ped, coords.x, coords.y, coords.z)
  return true
end
```

</details>

## Naming

### Constants

Lua `5.4` introduced the `<const>` keyword and as such only resources which fxmanifest sets `lua54 'yes'` should use `<const>`. This keyword is also limiting as once a variable is declared, it's [immutable](https://www.lua.org/manual/5.4/manual.html#3.3.7).
> Constants should be named using `UPPER_SNAKE_CASE`[^1], with *global* constants prefixed with an underscore.
>
> If available & applicable, the `<const>` keyword should be used as well to indicate that the variable is a constant.

[^1]: As a fun tidbit, conventionally named [SCREAMING_SNAKE_CASE](https://en.wikipedia.org/wiki/Snake_case).

<details>
<summary>Example</summary>

```lua
_MY_GLOBAL_CONSTANT = 1
local MY_LOCAL_CONSTANT = 0
```

</details>

### Variables

One of Lua's strengths is its dynamic typing and as such, it is important to name variables descriptively & unambiguously.
> As a general rule, a variable should be more descriptive as its scope gets larger. Using `i` and `k` inside a `for` loop is fine, but using them to describe a global variable is not.
>
> Variables should be named using `snake_case`, with *global* variables using `UPPER_SNAKE_CASE`[^1].
>
> - `boolean` variables should be prefixed with a verb, prefixed with `is`, `has`, `can`, etc.
> - `number`, `string` or `vector` variables should use a simple noun or adjective.
> - `index` or `key` variables should use `i` or `k`, respectively (moving towards `j`, `l`, etc. if needed).
> - `unused` variables should be named `_`.

<details>
<summary>Example</summary>

```lua
DEBUG_MODE = true
local is_player_dead = false
local player_ped = PlayerPedId()
local coords = GetEntityCoords(player_ped)

for i = 1, 10 do
  print(i)
end
```

</details>

### Objects

Objects are a powerful tool in Lua, storing all lua data types, allowing them to be arrays, objects, classes and more. Due to their multi-faceted nature, there are some conventions we should follow.
> Objects should be named using `PascalCase` if outside the scope of a function, and `camelCase` if inside the scope of a function.
>
> Arrays should be declared without number keys, unless needed.
>
> An objects name should be a plural noun, describing what the object is (where applicable).

<details>
<summary>Example</summary>

```lua
local Players = {
  {name = 'John', age = 21},
  {name = 'Jane', age = 22}
}

local function print_players(players)
  if type(players) ~= 'table' then return end
  for i = 1, #players do
  local player = players[i]
    print(player.name)
  end
end
```

</details>

### Functions

The Lua has 3 hidden beauties' that make it a powerful language. First is that functions are first class citizens, second is that its scope is global by default and the third is its automatic garbage collection, this makes uniformity in naming functions essential to let these features shine.
> Local functions should be named using `snake_case`, with *global* functions should be named using `PascalCase`.
>
> Functions should only be completing a single task and their name should reflect this.
>
> Functions should be decalred in precedence of their use, with all *local* functions at the top of the file, followed by *global* functions and then *class* functions.

<details>
<summary>Example</summary>

```lua
local TeleportedPlayers = {}

---@param ped integer The teleporting ped.
---@param target integer The target ped.
---@return boolean `true` if the player was teleported successfully.
local function teleport_to_ped(ped, target)
  local player_id = GetPlayerFromServerId(ped)
  local coords = GetEntityCoords(target)
  SetEntityCoords(ped, coords.x, coords.y, coords.z)
  TeleportedPlayers[player_id] = true
  return true
end

---@return boolean? `true` if the player has teleported.
function HasPlayerTeleported(player)
  return TeleportedPlayers[player]
end
```

</details>

> [!TIP]
> Most functions should be local, as they are not intended to be called from outside the file.

### Classes

Whilst Lua doesn't have classes, it does have objects and these objects can be manipulated to act like classes.
> Classes should be named using `PascalCase` and the methods should be named using `flatcase`.
>
> Classes should be prefixed with a `C` to indicate that it is a class.

<details>
<summary>Example</summary>

```lua
local CPlayer = {}
CPlayer.__index = CPlayer

function CPlayer.new()
  local self = setmetatable({}, CPlayer)
  return self
end

function CPlayer:teleportped(target)
  local player_id = GetPlayerFromServerId(self.ped)
  local coords = GetEntityCoords(target)
  SetEntityCoords(self.ped, coords.x, coords.y, coords.z)
  TeleportedPlayers[player_id] = true
  return true
end
```

</details>

> [!NOTE]
> Modules/packages are typically made with classes or class-like architecture however, are named using `flatcase` and should not be prefixed with `C`[^2]. This is to imitate the libraries that Lua uses, such as `math`, `string`, etc.

[^2]: This is usually still done when naming the main object in annotation, as it is a class-like object.
  ie. <!-- markdownlint-disable-next-line MD031 -->
  ```lua
  ---@class CInterval
  ```

### Events

Events are critical in FiveM as they are used to communicate between contexts by triggering a function in another context.
> Events should be named using `PascalCase` and should be prefixed with it's file name, as well as the context of the event:
>
>```lua
> '[resource]:[context]:EventName'
>```
>
> When declaring an event, there are three options depending on use and context:
>
> - `AddEventHandler` for local events (client <---> client or server <---> server).
> - `RegisterNetEvent` for Network events declared on the client.
> - `RegisterServerEvent` for Network events declared on the server.
>
> The handler function for each of the above should be used when declaring and if possible, use a local function to handle the event.

<details>
<summary>Example</summary>

```lua
---@param reason string The reason for kicking the player.
---@param source integer The player\'s server ID.
---@param resource string The resource name.
local function kick_player(reason, source, resource)
  DropPlayer(source, reason)
  print(('Player %s was kicked from %s for %s'):format(source, resource, reason))
end

RegisterServerEvent('don:server:KickPlayer', kick_player)
```

</details>

### Files

Files are the main building blocks of any script or software, making knowing what you're working with all the more important.
> Files should be named using `snake_case` and they should either:
>
> - Be housed in a `client`, `server` or `shared` folder, and named accordingly.
> - If not housed in a folder, be prefixed with `cl_`, `sv_` or `sh_` to indicate the context of the file.

### Git

Git is a powerful tool, and when used correctly can make the development process much smoother.

#### Commits

Commits are the bread & butter of git and as such should be descriptive & concise.
> Commits follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/#summary) standard, and should be prefixed with one of the following:
> | Type | Usage |
> | --- | --- |
> | `build` | Changes that affect the build system or external dependencies (example scopes: gulp, broccoli, npm). |
> | `chore` | Changes that don't modify src or test files. |
> | `docs` | Documentation only changes. |
> | `feat` | A new feature. |
> | `fix` | A bug fix. |
> | `perf` | A code change that improves performance. |
> | `refactor` | A code change that neither fixes a bug nor adds a feature. |
> | `revert` | Reverts a previous commit. |
> | `style` | Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc). |
> | `test` | Adding missing tests or correcting existing tests. |
>
> The commit message should be in the following format: `<type>(<scope>): <description>`.
>
> - `type` is the type of commit, as described above.
> - `scope` is the scope of the commit, and is optional.
>   - Where scope is the relative path to the file that the commit is affecting.
> - `description` is a short description of the commit.
>
> If any changes introduce a breaking change, the commit should has its type (and scope) suffixed with a `!` and the a footer with the breaking change.
>
> ```txt
> > ![CAUTION]
> > BREAKING CHANGE: <description>
> ```

<details>
<summary>Example</summary>

```txt
feat(client): Add TeleportToPed Fn

Add a function to teleport the player to a ped.
```

```txt
revert(client)!: Revert Add TeleportToPed Fn

> ![CAUTION]
> BREAKING CHANGE: This reverts commit 123456.
```

</details>

#### Versioning

Versioning is a critical part of any software and as such should be done correctly.
> Versioning should follow the [Semantic Versioning](https://semver.org/) standard and should be in the following format: `MAJOR.MINOR.PATCH`.
>
> - `MAJOR` version when you make incompatible API changes.
> - `MINOR` version when you add functionality in a backwards compatible manner.
> - `PATCH` version when you make backwards compatible bug fixes.
>
> Pre-release versions should be suffixed with a `-` and the pre-release version and build metadata should be suffixed with a `+` and the build metadata.
>
> The version should be stored in the `fxmanifest.lua` file and should be updated with each release.

<details>
<summary>Example</summary>

```lua
fx_version 'cerulean'
game 'gta5'

author 'DonHulieo'
description 'A Style Guide for Lua in FiveM'
url 'https://github.com/DonHulieo/lua-style-guide'
version '1.0.0'
```
