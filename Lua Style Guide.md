# FiveM Lua Style Guide

This guide is intended to help you write Lua code that is consistent and easy to read. It is based on the [Lua Style Guide](http://lua-users.org/wiki/LuaStyleGuide), [FiveM Style Guide for Lua](https://github.com/project-error/fivem-lua-style) and [Effective FiveM Lua](https://manason.github.io/effective-fivem-lua/).

## Table of Contents

* [Formatting](#formatting)
  * [Indentation](#indentation)
  * [Line Length](#line-length)
  * [Whitespace](#whitespace)
  * [Comments](#comments)
* [Naming](#naming)
  * [Variables](#variables)
  * [Functions](#functions)
  * [Tables](#tables)

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

The maximum line length should be 160 characters. This is to ensure that the code is readable and does not go off the screen. If you need to go over 160 characters, then you should consider breaking the line up into multiple lines. One way of eaching this is to use a multi-line string, using the `[[` and `]]` syntax. Another way is to use a table, and then use the `table.concat` function to join the table together.

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

Well written code should be self-documenting. However, there are times where you need to add comments to explain what the code is doing. You should use comments to explain why the code is doing something, rather than what the code is doing. For example, you should not write a comment that says `-- set the variable x to 5`. Instead, you should write a comment that says `-- timmy wants 5 apples`. When writing comments, you should use `--` for single line comments, and `--[[` and `]]` for multi-line comments and the code should be indented and whitespace should be used to separate the comment from the code.

You should also use comments to separate code into logical sections, for example, you should use comments to separate the `local functions`, `Threads`, `Event Handlers`, etc. Lastly, you should utilise the `---@class`, `---@param`, and `---@return` tags (at least) when writing comments. These tags are used by the [lua-language-server](https://github.com/LuaLS/lua-language-server/wiki/Annotations) to provide code completion and documentation.

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

### Variables

As a general rule, a variable should be more descriptive as it's scope gets larger. While `k` is a perfectly fine variable name for a for loop, it is not a good variable name for a global variable. This is because the scope of a global variable is the entire script, and it is not clear what `k` is.

Well named variables reduce the need for comments, and make the code more readable. This is why it is important to name variables descriptively. Variables holding a Boolean value should be prefixed with a verb to distinguish them from variables holding other types of values. For example, `isDead` is a good variable name for a Boolean variable, but `dead` is not. This is because `dead` could be a number, a string, a table, etc. Constants should be written in `SNAKE_CASE`, no matter what the scope is.

When naming variables, you should use `camelCase` for local variables, and `SNAKE_CASE` for global variables prefixed with an underscore (the exception to this is tables, explained below).

```lua
-- Bad
SV = 1
local sv = 1
local dead = false

-- Good
_SOME_VARIABLE = 1
local SOME_VARIABLE = 1
local isDead = false
```

### Functions

The Lua has 3 hidden beauties' that make it a powerful language. The first is that functions are first class citizens, the second is that it's scope is global by default and the third is it's automatic garbage collection. This means that you can assign a function to a variable, and then call that variable as a function. This is why it is important to name functions descriptively, but this means that functions that are not local are *global by default*, most functions in a script *do not* need a global scope, and should therefore be local.

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

### Tables

Tables are a powerful tool in Lua, and are used to store data. Tables are also used to store functions, and are used to create classes. Tables should be named using `PascalCase`, no matter what the scope is. When naming tables, you should use a noun to describe what the table is. For example, `Peds` is a good table name, but `spawned` is not. This is because `spawned` doesn't inherently tell us *what* has spawned.

```lua
-- Bad
local spawned = {}

-- Good
local Peds = {}
```
