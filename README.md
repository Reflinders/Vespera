# Vespera
Vespera is a network framework that minimizes the size of packets. 
Vespera works as an emulator of Roblox's Remote- Events &amp; Functions

## Explanation

You may be asking: how does vespera work? In a nutshell, Vespera acts as a middleman that compresses and then decompresses data on both ends. Vespera compresses tables & strings. 
Additionally, Vespera has a 60 calls/second rate limit on the server (and a 8 invocation/second rate).

## Starting

To start, simply call the `start` function of Vespera:

```lua
local Vespera = require(VesperaModule)
Vespera.start()
```

## Chains

Chains are an alias for the Events that Vespera creates. To create a chain, call `tether` of Vespera:

```lua
local newChain = Vespera.tether('insertName')
```

After creating a chain, you can connect to it, and if the chain is hosted on the server, you can add an invocation response:

```lua
newChain:Connect(function()
end)

-- ONLY WORKS ON THE SERVER
newChain:OnInvoke(function()
end)
```

To fire the event, call `Fire` of the respective chain. On the server, the first argument is/are the receiver(s). The receiver can be passed on as a player, table of players, or a function returning a table of players. There are also player containers that can be accessed through `ServerChain.Receivers`. The containers include: All, which returns all players; Only, which returns a table of the players passed; Except, which returns a table of all players except of those passed; and InRange, which returns a table of all players who are in `y` range of `x`. Anywho:

```lua
-- On the server
newChain:Fire(newChain.Receivers.All, 'Hi')
newChain:Fire(newChain.Receivers.All(), 'Hi')
newChain:Fire(game.Players:GetPlayers(), 'Hi')
-- All of the above statements are practically the exact same thing !

--/ ...

-- On the client
newChain:Fire('Hi')

-- You can also invoke the server, which returns a promise
local a; newChain:Invoke():andThen(function(v)
  print("Retrieved from server!")
  a = v
end)
```

## Lots

A Lot is essentially a batch of associated Chains. Create a Lot via `Vespera.lot`. The first argument is the Lot's Reference-Key and the second is an array of Chain-Aliases you want to make. It should look like this:

```lua
local Lot = Vespera.lot("MyLot", {"PressA", "PressB"})

Lot.PressA:Fire(Vespera.Recv.All)

Lot:Destroy()
```

A Lot is quite basic — It's just a dictionary of chains with a `Destroy` method that, when called, removes all Chains associated with the Lot. You can also fetch existing lots by omitting the second argument and only entering the Lot's Reference-Key.

```lua
-- Script 1
local Lot = Vespera.lot("Code", {"hi", "yup"})

-- Script 2
local Lot = Vespera.lot("Code") -- Will fetch the already existing one; any attempt to overwrite an existing one will fail!
```

## Preservation

Because of how Roblox's remote- events and functions work, arrays that are out-of-order, mixed tables, and functions cannot be exchanged between server & client. And so to fix that solution, I've implemented two systems: `Preserve` and `Task`. First, I'll go over `Preserve`, then I'll discuss `Task` — `Preserve` (access through `Vespera.Preserve`) is used to preserve mixed tables. Simply pass the mixed table as the first argument and Vespera will do the rest of the work. `Task` (access through `Vespera.Task`) is used to preserve functions. The first argument is the ModuleScript in which the function can be found and the second argument is the key/alias of the function. Refer to the figures below:

```lua
-- Using Preserve
-- A mixed table is essentially a table with both k-v and i-v pairs, or in other words, a table that is both a dictionary and array at the same time.
local Preserve = Vespera.Preserve

Chain:Fire(Chain.Receivers.All, Preserve {
  'Welcome',
  'To the script!',
  Yes = 'f'
}) -- This will be exactly the same when received on the client (or at least should be ... if any bugs are encountered, make an issue on this page!)
```


```lua
-- Using Task

-- / ... ON THE SERVER

local Task = Vespera.Task

Chain:Fire(Chain.Receivers.All, Task(ModuleScript, 'SayHello'))

-- / ... ON THE CLIENT

Chain:Connect(function(foo)
  foo() -- Will be sent as a function!
  -- Logically, this should print out 'Hello, World!' in the console
end)

```

```lua
-- Here's the module script used in the figure above
return {
  SayHello = function()
    print 'Hello, World!'
  end
}
```
