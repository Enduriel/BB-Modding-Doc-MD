Mod queuing is essential to preserving good compatibility between mods. The basic idea is that certain mods need to load after/before other mods to preserve compatibility. For example, mods relying on [MSU](https://github.com/MSUTeam/MSU) should generally load after it, otherwise if they tried to overwrite the `onEquip` function in skills (a function that is only added to skills by MSU), their hooks would fail due to the function not being added yet.

## Changes From Modding Script Hooks
Modern Hooks has two improvements to the queuing system over [Modding Script Hooks](https://www.nexusmods.com/battlebrothers/mods/42). The first is the availability of queue [[#Buckets]]. The second is a more advanced queuing algorithm, which is able to resolve situations where two parts of a mod need to queue in different positions relative to another mod. For example, if you'd like to queue part of your mod before MSU, and part after, this is impossible with Modding Script Hooks, but with Modern Hooks, you can simply [[Mod Object#Queuing a Function|queue]] one function before `mod_msu`, and the other after, and the queueing algorithm will handle the rest.

Additionally, Compatibility Data (requirements and incompatibilities) is separate from load order data (whether you'd like a part, or all, of your mod to load before/after a different mod), which means it is also possible to require a mod but load before it.


## Buckets
Buckets are used to [[Mod Object#Queuing a Function|queue functions]] separately from the normal flow, this is an advanced modding technique that should probably only be used if you're making a library or large overhaul. Sometimes you'd like to queue a specific part of your (or your entire) mod before or after all normal mod hooks. With the basic queuing system this is impossible, which is where buckets come in. 

In principle, buckets are just a way to group functions that should be queued together. By default, all functions are in the `::Hooks.QueueBucket.Normal` bucket. There are currently 5 normal Buckets:
```squirrel
::Hooks.QueueBucket.First
::Hooks.QueueBucket.Early
::Hooks.QueueBucket.Normal
::Hooks.QueueBucket.Late
::Hooks.QueueBucket.Last
```
with all the functions in a bucket lower on the list being sorted and executed after those in a bucket higher on the list.

To use buckets you must [[Mod Object#Queuing a Function|queue your functions]] and add the bucket you'd like to be in as a final argument.
### AfterHooks
```squirrel
::Hooks.QueueBucket.AfterHooks
```
is a special bucket that is run *after* hooks are executed, this is intended to give your mod a place to initialize [Squirrel Classes](TODO) and [Battle Brothers Classes](TODO) while allowing them to still be hooked. This is important because if you initialize a Squirrel Class it can no longer be hooked at all, while if you initialize a BB Class and it is later hooked, the hooks will not apply to your object.
