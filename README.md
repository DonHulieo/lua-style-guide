# FiveM Lua Style Guide

This guide is intended to help you write Lua code that is consistent and easy to read. It is based on the [Lua Style Guide](http://lua-users.org/wiki/LuaStyleGuide), [FiveM Style Guide for Lua](https://github.com/project-error/fivem-lua-style) and [Effective FiveM Lua](https://manason.github.io/effective-fivem-lua/).

## Table of Contents

* [Formatting](#formatting)
  * [Indentation](#indentation)
  * [Line Length](#line-length)
  * [Whitespace](#whitespace)
  * [Comments](#comments)
* [Naming](#naming)
  * [Constants](#constants)
  * [Variables](#variables)
  * [Tables](#tables)
  * [Functions](#functions)
  * [Classes](#classes)
  * [Threads](#threads)
  * [Events](#events)
  * [Files](#files)
* [Code Structure](#code-structure)
  * [Functions](#function-structure)
  * [Threads](#thread-structure)
  * [Events](#event-structure)
  * [Tables](#table-structure)
  * [Classes](#class-structure)
* [Optimisation](#optimisation)
  * [Caching](#caching)
  * [Global Variables](#global-variables)
  * [Garbage Collection](#garbage-collection)
* [Credits](#credits)

## Formatting

### Indentation

Tabs whilst being the most common, are not the best way to indent code. The reason for this is that tabs are not consistent across all editors. For example, if you have a tab set to 4 spaces in your editor, but someone else has it set to 2 spaces, the code losses it's consitency. This poses an issue as well when uploading code to GitHub, as GitHub will display tabs as 8 spaces. This is why spaces are preferred over tabs.

The *Programming in Lua*, *The Lua Refrence Manual* and *Project Error* all use 2 spaces for indentation. This I believe will serve as the best indentation for two reasons. Firstly, Lua is inherently a heavily nested language, and 2 spaces allows for more code to be displayed on the screen at once. Secondly, 2 spaces alleviates the issue of tabs being inconsistent across editors.

```lua
-- Bad
function foo()
∙∙∙∙print('bar')
end

-- Good
function foo()
∙∙print('bar')
end
```

### Line Length

The maximum line length should be 160 characters. This is to ensure that the code does not go off the screen, this is not a *strict* rule however, more of an aim. If you need to go over 160 characters, then you should consider breaking the line up into multiple lines. One way of eaching this is to use a multi-line string, using the `[[` and `]]` syntax. Another way is to use a table, and then use the `table.concat` function to join the table together, some events also take a table as a parameter, so you can use that as well.

```lua
-- Bad
local someTable = { 'this is a very long string that goes over 160 characters and is very hard to read', 'this is a very long string that goes over 160 characters and is very hard to read' }

-- Good
local someTable = {
  'this is a very long string that goes over 160 characters and is very hard to read',
  'this is a very long string that goes over 160 characters and is very hard to read'
}
```

### Whitespace

Whitespace is used to separate keywords, operators, and other elements. It is also used to make the code more readable. For example, you should put a space after a comma, and before and after an operator. You should also put a space after a keyword, such as `if`, `for`, `while`, etc. However, you should not put a space after a function name, or before and after a parenthesis.

```lua
-- Bad
local someTable = {'foo','bar'} or local someTable = { 'foo', 'bar' }
local someTable = {foo='bar'} or local someTable = { foo = 'bar' }
local someNumber = 1+1 
local function foo()end or local function foo ()end

-- Good
local someTable = {'foo', 'bar'}
local someTable = {foo = 'bar'}
local someNumber = 1 + 1
local function foo() end
```

### Comments

Well written code should be self-documenting. However, there are times where you need to add comments to explain what the code is doing. You should use comments to explain why the code is doing something, rather than what the code is doing, unless absolutely neccessary. For example, you should not write a comment that says `-- set the variable x to 5`. Instead, you should write a comment that says `-- if timmy wants 5 apples`. When writing comments, you should use `--` for single line comments, and `--[[` and `]]` for multi-line comments and the code should be indented and whitespace should be used to separate the comment from the code.

You should also use comments to separate code into logical sections, for example, you should use comments to separate the `functions`, `Threads`, `Event Handlers`, etc. Lastly, you should utilise the `---@class`, `---@param`, and `---@return` tags (at least) when writing comments. These tags are used by the [lua-language-server](https://github.com/LuaLS/lua-language-server/wiki/Annotations), and provide useful information to the server, such as the type of a variable, the type of a parameter, and the type of a return value.

```lua
-- Bad
local function helloWorld() -- this function prints hello world
  print('hello world')
end

RegisterNetEvent('helloWorld', function() -- this event calls the helloWorld function
  helloWorld()
end)

-- Good
local function helloWorld() -- this function prints hello world
  print('hello world')
end

---------------------------------------------------------------------- EVENTS ----------------------------------------------------------------------

RegisterNetEvent('print:helloWorld', function() -- this event calls the helloWorld function
  print('hello world')
end)
```

## Naming

### Constants

Lua doesn't provide a way to declare constants, so to differentiate constants from variables, constants should be written in `SNAKE_CASE`. Global constants should be prefixed with an underscore, this allows us to easily access variables and constants in the global scope, using `global` or `_G`. While this is not a foolproof way to differentiate constants from variables, it's better than the comments of hope.

```lua
-- Bad
someVariable = 5
local someOtherVariable = 5

-- Good
_SOME_VARIABLE = 5
local SOME_OTHER_VARIABLE = 10
```

When naming constants, you should use the following rules:

* Use `UPPER_SNAKE_CASE` for constants
* Prefix global constants with an underscore

### Variables

As a general rule, a variable should be more descriptive as it's scope gets larger. While `k` is a perfectly fine variable name for a key in a for loop, it is not a good variable name for a boolean, where it's not immediately apparent what that boolean represents.

Well named variables reduce the need for comments, and make the code more readable. This is why it is important to name variables descriptively. When naming variables, you should use `camelCase` for local variables, and `SNAKE_CASE` for global variables prefixed with an underscore (the exception to this is tables, explained below).

```lua
-- Bad
SV = 1
local S_O_V = 0
local dead = false

-- Good
_SOME_VARIABLE = 1
local someOtherVariable = 0
local isDead = false
```

When naming variables, you should use the following rules:

* Use `camelCase` for local variables
* Use `SNAKE_CASE` for global variables prefixed with an underscore
* Avoid using single letter variable names or abbreviations, except for `i` and `k`
* When the variable is unused, use `_` as the variable name
* When the variable is a boolean, prefix it with a verb
* When the variable is a number, string or vector, use a simple noun or adjective
* When the variable represents an index or key, use `i` or `k`

### Tables

Tables are a powerful tool in Lua, and are used to store data. Tables are also used to store functions, and are used to create classes. Tables should be named using `PascalCase`, no matter what the scope is. When naming tables, you should use a plural noun to describe what the table is. For example, `Peds` is a good table name, but `spawned` is not. This is because `spawned` doesn't inherently tell us *what* has spawned.

```lua
-- Bad
local spawned = {}

-- Good
local Peds = {}
```

When naming tables, you should use the following rules:

* Use `PascalCase` for tables
* Use a plural noun to describe what the table is

### Functions

The Lua has 3 hidden beauties' that make it a powerful language. The first is that functions are first class citizens, the second is that it's scope is global by default and the third is it's automatic garbage collection. That means we can assign a function to a variable, and then call that variable as a function. This is why it is important to name functions descriptively, but this means that functions that are not local are *global by default*, most functions in a script *do not* need a global scope, and should therefore be local.

Local functions should be named using `camelCase`, Global functions however should be named using `PascalCase`. When naming functions, you should use a verb to describe what the function does. For example, `printHelloWorld` is a good function name, but `helloWorld` is not. This also helps with describing what the function returns (if anything at all).

```lua
-- Bad
local function helloWorld()
  print('hello world')
end

function dead?()
  if health == 0 then
    return true
  end
  return false
end

-- Good
local function printHelloWorld()
  print('hello world')
end

function IsPlayerDead()
  if health == 0 then
    return true
  end
  return false
end
```

When naming functions, you should use the following rules:

* Use `camelCase` for local functions
* Use `PascalCase` for global functions
* Use a verb to describe what the function does
* Use a noun to describe what the function returns (if anything at all)

### Classes

Whilst Lua doesn't have classes, it does have tables, and we can use tables to create classes. When creating a class, you should use `PascalCase` for the class name, and `camelCase` for the constructor name. When naming classes, you should use a noun to describe what the class represents and prefix it with `C`. For example, `CPlayer` is a good class name, but `player` is not. This is because `player` could be a variable representing a player, or a function that returns a player.

```lua
-- Bad
local player = {}

function player.new()
  local self = {}
  return self
end

-- Good

local CPlayer = {}

function CPlayer.new()
  local self = {}
  return self
end
```

When naming classes, you should use the following rules:

* Use `PascalCase` for the class name
* Use `camelCase` for the constructor name
* Use a noun to describe what the class represents and prefix it with `C`

### Threads

Threads in FiveM allow tasks to be run asynchronously, and are used to run tasks in the background. This poses a performance issue, as threads are not garbage collected, and can cause memory leaks. Threads which aren't intended to be run in the background should be named using `camelCase`, and threads which are intended to be run in the background should declared using without a name using `CreateThread(function() end)`. This is because threads which are intended to be run in the background are not intended to be called, and therefore don't need a name.

```lua
-- Bad
CreateThread(function()
  while true do
    Wait(0)
    local player = PlayerPedId()
    local coords = GetEntityCoords(player)
    local heading = GetEntityHeading(player)
    print('x: ' .. coords.x .. ' y: ' .. coords.y .. ' z: ' .. coords.z .. ' heading: ' .. heading)
  end
end)

-- Good
local isPrintCoordsRunning = false
local function printCoords()
  isPrintCoordsRunning = true
  CreateThread(function()
    while isPrintCoordsRunning do
      Wait(0)
      local player = PlayerPedId()
      local coords = GetEntityCoords(player)
      local heading = GetEntityHeading(player)
      print('x: ' .. coords.x .. ' y: ' .. coords.y .. ' z: ' .. coords.z .. ' heading: ' .. heading)
    end
  end)
end
```

When naming threads, you should use the following rules:

* Use `camelCase` for threads which aren't intended to be run in the background
* Use `CreateThread(function() end)` for threads which are intended to be run in the background

### Events

Events are used to communicate between the client and server, and are used to trigger functions on the other side. Events should be named using `PascalCase`, and should be prefixed with `:server:` or `:client:` depending on which side the event is triggered on. When naming events, you should use a verb to describe what the event does. For example, `:server:KickPlayer` is a good event name, but `:server:kick` is not. This is because `:server:kick` doesn't inherently tell us *what* is being kicked.

```lua
-- Bad
RegisterNetEvent('kick')
AddEventHandler('kick', function()
  DropPlayer(source, 'You have been kicked')
end)

-- Good
RegisterNetEvent('don:server:KickPlayer', function()
  DropPlayer(source, 'You have been kicked')
end)
```

When naming events, you should use the following rules:

* Use `PascalCase` for events
* Use a verb to describe what the event does
* Prefix events with `:server:` or `:client:` depending on which side the event is triggered on

### Files

Files are the main building blocks of any script or software, and it is important to name them descriptively. Files should be named using `LowerSnakeCase`, and should be prefixed with `cl_` or `sv_` depending on which side the resource is intended to run on. When naming resources, you should use a noun to describe what the resource is. For example, `cl_player` is a good resource name, but `player` is not. Preferreably, you should separate the resource into it's respective `client`, `server` and `shared` folders, and then name the files accordingly, without their prefix. This is because it is easier to find the file you are looking for, and it is easier to tell what the file does.

When naming files, you should use the following rules:

* Use `LowerSnakeCase` for files
* Use a noun to describe what the resource is
* Prefix files with `cl_` or `sv_` depending on which side the resource is intended to run on
* Preferreably, you should separate the resource into it's respective `client`, `server` and `shared` folders, and then name the files accordingly, without their prefix

## Code Structure

### Function Structure

Functions are pivotal to any script, and it is important to structure them in a way that is easy to read and understand. Local functions should be declared at the top of the file, in the order that they are called, and then global functions after that. Functions should only perform one task, and by doing so, avoid nesting. This is because nesting makes the code harder to read, and harder to understand. Functions should also be commented, and should be commented using `---` instead of `--`. This is because `---` is used by some IDEs to generate documentation for the function.

```lua
---@param player number The players Ped ID
---@param coords table The coordinates to teleport the player to
---@return boolean Returns true if the player was teleported successfully
local function teleportPlayer(player, coords)
  SetEntityCoords(player, coords.x, coords.y, coords.z)
  return true
end
```

When structuring functions, you should use the following rules:

* Local functions should be declared at the top of the file, in the order that they are called, and then global functions after that
* Functions should only perform one task, and by doing so, avoid nesting
* Functions should be commented using `---` instead of `--`

### Thread Structure

While global threads have no official structure, local threads do based on the nature of them (basically being a fancy local function). Local threads should be declared after local functions, and should be declared in the order that they are called. Threads should also be commented, and should be commented using `---@async` to indicate that the thread is asynchronous. This is because `---@async` is used by some IDEs to generate documentation for the thread. Local threads should also be commented using `---` for any additional information.

```lua
---@param player number The players Ped ID
---@async
--- Checks if the player has died, and sets a variable to true to break the thread if they have
local playerHasDied = true
local function checkPlayerDeath(player)
  playerHasDied = false
  CreateThread(function()
    while not playerHasDied do
      Wait(0)
      if IsEntityDead(player) then
        playerHasDied = true
        break
      end
    end
  end)
end
```

When structuring threads, you should use the following rules:

* Local threads should be declared after local functions, and should be declared in the order that they are called
* Local threads should be commented using `---` instead of `--`
* All threads should be commented using `---@async` to indicate that it is an asynchronous task

### Event Structure

Events should be declared after all functions and local threads, and should be declared in the order that they are called. Events have five important syntax rules, depending on the use case. The first rule is for events across the network (client <---> server), they must be registered using `RegisterNetEvent` or `RegisterServerEvent`, depending on which side the event is triggered on. The second rule is for events that trigger locally (client <---> client ot (server <---> server), only need to be registered by using `AddEventHandler`. The third rule for triggering events across the network is that they must be triggered using `TriggerServerEvent` or `TriggerClientEvent`, depending on which side the event is triggered on. The fourth rule for triggering events locally is that they must be triggered using `TriggerEvent`. The fifth rule, and most important for readability, is that events should only be declared once, and should follow the syntax relevant to their scope.

```lua
---@param reason string The reason for kicking the player
RegisterServerEvent('don:server:KickPlayer', function(reason)
  TriggerClientEvent('don:client:KickPlayer', source, reason)
end)

---@param reason string The reason for kicking the player
RegisterNetEvent('don:client:KickPlayer', function(reason)
  DropPlayer(source, reason)
end)

---------------------------------------- LOCAL EVENTS ----------------------------------------

---@param player number The players Ped ID
local function checkPlayerDeath(player)
  CreateThread(function()
    while true do
      Wait(0)
      if IsEntityDead(player) then
        TriggerEvent('don:client:PlayerHasDied', player)
        break
      end
    end
  end)
end

---@param player number The players Ped ID
AddEventHandler('don:client:PlayerHasDied', function(player)
  print('Player has died')
end)
```

When structuring events, you should use the following rules:

* Events should be declared after all functions and local threads, and should be declared in the order that they are called
* Events across the network (client <---> server) must be registered using `RegisterNetEvent` or `RegisterServerEvent`, depending on which side the event is triggered on
* Events that trigger locally (client <---> client ot (server <---> server), only need to be registered by using `AddEventHandler`
* Events across the network must be triggered using `TriggerServerEvent` or `TriggerClientEvent`, depending on which side the event is triggered on
* Events locally must be triggered using `TriggerEvent`
* Events should only be declared once, and should follow the syntax relevant to their scope

### Table Structure

Tables are used to store data, and should be used to store data that is used across multiple functions. Tables should be declared at the top of the file, and should be declared in the order that they are used. Tables can be accessed using `table.key` or `table['key']`, but the former is preferred. Tables should also be commented, and should be commented using `---` instead of `--`. This is because `---` is used by some IDEs to generate documentation for the table.

```lua
--- The table that stores all the players data
local Players = {}
```


When structuring tables, you should use the following rules:

* Tables should be declared at the top of the file, and should be declared in the order that they are used
* Tables can be accessed using `table.key` or `table['key']`, but the former is preferred
* Tables should be commented using `---` instead of `--`

### Class Structure

-- TODO

## Optimisation

### Caching

Caching is the process of storing data in a variable, to avoid having to retrieve it again. This is important for optimisation, as it reduces the amount of times that the game has to retrieve data. Caching should be used for any data that is used multiple times, and should be used for any data that is used in a loop. Caching should be done using `local` variables, and should be done at the top of the file, or at the top of the function.

```lua
local player = PlayerPedId()
local coords = GetEntityCoords(player)
```

When caching data, you should use the following rules:

* Caching should be done using `local` variables, and should be done at the top of the file, or at the top of the function
* Caching should be used for any data that is used multiple times, and should be used for any data that is used in a loop

### Global Variables

Caching is a perfect introduction to global variables, as they are what caching is trying to resolve. Global variables are variables that are declared outside of a function, and can be accessed by any function. This is bad practice, as it can cause issues with other scripts, and can cause issues with the script itself. Global variables should be avoided at all costs, and should only be used for data that is used across multiple files.

When using global variables, you should use the following rules:

* Unless absolutely necessary, global variables should be avoided at all costs

### Garbage Collection

Lua handles garbage collection natively, however, it is still good practice to empty variables that are no longer needed. This is because Lua will still have to perform garbage collection on the variable, and will still have to perform garbage collection on the data that the variable is storing. This can cause performance issues, and can cause memory leaks. This can be avoided by setting the variable to `nil`, or by using `table.remove` if the variable is a table key or value. This will remove the variable from memory, and will allow Lua to perform garbage collection on the variable.

```lua
local player = PlayerPedId()
local coords = GetEntityCoords(player)

-- Remove the variables from memory
player = nil
coords = nil
```

While this serves well for smaller datasets, it's harder to manage on a larger datasets. For example, we have a function that sorts a table of vectors, clockwise from a given point. (If not using the table.sort method) This operation will cache the orginal table, and will create a new table to store the sorted data. As well as a variable to store the angle of the vector. This will result in 3 variables being cached, and 3 variables being removed from memory.

```lua
local function SortTable(t, center)
    local sorted = {}
    local function GetAngle(p)
        local angle = math.atan2(p.y - center.y, p.x - center.x)
        if angle < 0 then
            angle = angle + math.pi * 2
        end
        return angle
    end
    for i = 1, #t do
        local inserted = false
        for j = 1, #sorted do
            if GetAngle(t[i]) < GetAngle(sorted[j]) then
                table.insert(sorted, j, t[i])
                inserted = true
                break
            end
        end
        if not inserted then
            sorted[#sorted + 1] = t[i]
        end
    end
    return sorted
end

RegisterNetEvent('don:client:SortTable', function(table)
    local sorted = SortTable(table, vector3(0, 0, 0))
    print(sorted)
    collectgarbage("collect")
end)
```

When removing variables from memory, you should use the following rules:

* Variables should be removed from memory using `nil` or `table.remove`
* Collection garabage after large operations to prevent memory leaks

## Credits

* [FiveM Documentation](https://docs.fivem.net/docs/scripting-reference/)
* [Lua 5.1 Refernece Manual](http://www.lua.org/manual/5.1)
* [Lua Style Guide](http://lua-users.org/wiki/LuaStyleGuide)
* [Effective FiveM Lua](https://manason.github.io/effective-fivem-lua/)
* [FiveM Style Guide for Lua](https://github.com/project-error/fivem-lua-style)
* [Lua Language Server](https://github.com/LuaLS/lua-language-server/wiki)
