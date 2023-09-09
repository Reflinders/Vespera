# Vespera
Vespera is a network framework that attempts to minimize the size of packets; works as an emulator of Roblox's Remote- Events &amp; Functions

## Explanation

You may be asking: how does vespera work? In a nutshell, vespera acts as a middleman that compresses and then decompresses data on both ends. Vespera compresses tables & strings. 
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
newChain:Fire(newChain.Receivers.All, 'hi')
newChain:Fire(newChain.Receivers.All(), 'hi')
newChain:Fire(game.Players:GetPlayers(), 'hi')
-- All of the above statements are practically the exact same thing !

--/ ...

-- On the client
newChain:Fire('hi')

-- You can also invoke the server, which returns a promise
local a; newChain:Invoke():andThen(function(v)
  print("Retrieved from server!")
  a = v
end)
```
