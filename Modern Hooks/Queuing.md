Mod queuing is essential to preserving good compatibility between mods. The basic idea is that certain mods need to load after/before other mods to preserve compatibility. For example, mods relying on [MSU](https://github.com/MSUTeam/MSU) should generally load after it, otherwise if they tried to overwrite the `onEquip` function in skills (a function that is only added to skills by MSU), their hooks would fail due to the function not being added yet.

## Changes From Modding Script Hooks
Modern Hooks has two improvements to the queuing system over [Modding Script Hooks](https://www.nexusmods.com/battlebrothers/mods/42). The first is the availability of queue [[#Buckets]]. The second is a more advanced queuing algorithm, which is able to resolve situations where two parts of a mod need to queue in different positions relative to another mod. For example, if you'd like to queue part of your mod before MSU, and part after, this is impossible with Modding Script Hooks, but with Modern Hooks, you can simply [[Mod Object#Queuing a Function|queue]] one function before `mod_msu`, and the other after, and the queuing algorithm will handle the rest.

Additionally, Compatibility Data (requirements and incompatibilities) is separate from load order data (whether you'd like a part, or all, of your mod to load before/after a different mod), which means it is also possible to require a mod but load before it.


## Buckets
Buckets are used to [[Mod Object#Queuing a Function|queue functions]] separately from the normal flow, this is an advanced modding technique that should probably only be used if you're making a library or large overhaul. Sometimes you'd like to queue a specific part of your (or your entire) mod before or after all normal mod hooks. With the basic queuing system this is impossible, which is where buckets come in. 

In principle, buckets are just a way to group functions that should be queued together. By default, all functions are in the `::Hooks.QueueBucket.Normal` bucket. There are currently 7 normal Buckets:
```squirrel
::Hooks.QueueBucket.First
::Hooks.QueueBucket.VeryEarly
::Hooks.QueueBucket.Early
::Hooks.QueueBucket.Normal
::Hooks.QueueBucket.Late
::Hooks.QueueBucket.VeryLate
::Hooks.QueueBucket.Last
```
with all the functions in a bucket lower on the list being sorted and executed after those in a bucket higher on the list.

To use buckets you must [[Mod Object#Queuing a Function|queue your functions]] and add the bucket you'd like to be in as a final argument.
### AfterHooks
```squirrel
::Hooks.QueueBucket.AfterHooks
```
is a special bucket that is run *after* hooks are executed, this is intended to give your mod a place to initialize [Squirrel Classes](TODO) and [Battle Brothers Classes](TODO) while allowing them to still be hooked. This is important because if you initialize a Squirrel Class it can no longer be hooked at all, while if you initialize a BB Class and it is later hooked, the hooks will not apply to your object.
### FirstWorldInit
```squirrel
::Hooks.Queuebucket.FirstWorldInit
```
is a special bucket run the first time a player loads or starts a game. This is intended to allow you to initialize squirrel classes which may require things like `::World.Assets` to exist.

### Which buckets should I use?
If you're a bit confused about all these buckets, that's honestly pretty understandable. The most important ones that you might genuinely end up using are `Normal`, `AfterHooks` and `FirstWorldInit`. `Normal` is where your functions get queued by default so don't worry about that.

As for [[#AfterHooks]] and [[#FirstWorldInit]], you can read the relevant sections, but the simple version is that if you are instantiating global [[Battle Brothers Class|BB Classes]] or [Squirrel Classes](https://developer.electricimp.com/squirrel/squirrel-guide/classes) for your mod, you should be doing that in a function in `FirstWorldInit`, and if that doesn't work for you due to your need to instantiate things before the world is loaded (mainly to modify the main menu), then you should use `AfterHooks`.

The other Buckets are there mostly for libraries or large mods which change core functionality in a serious way and *need* ***specific hooks*** to go first/last. You should not be queueing all your hooks in these buckets. These hooks should generally aim to not change core functionality of the function. My personal recommendation is the following:
- `Early`/`Late` should be used by large mods  (Legends, Reforged, PTR, etc) to add specific ***wrappers without modifying core functionality***
- `VeryEarly`/`VeryLate` should be used by mod libraries (MSU and other stuff like it) to add specific ***wrappers without modifying core functionality***
- `First`/`Last` should be ***reserved for Modern Hooks*** and anyone who wishes to directly mess with its behavior. You should *really* know what you're doing if you touch these.