[[Introduction#Registering your mod|::Hooks.register]] returns an instance of Modern Hooks' Mod [Squirrel Class](https://developer.electricimp.com/squirrel/squirrel-guide/classes).

# Requirement and Incompatibilities
In Modern Hooks, mod compatibility information is separate from queuing information, though declaring a mod as a requirement or stating that your mod is incompatible is still very simple.
## Require
```squirrel
<Mod>.require(...)
// ... is an arbitrary number of string parameters
```
Each parameter is a string in the form `modID operator version (Friendly Mod Name) [Compatibility Explanation]`, with everything except the modID being optional, and operator and version being required together.

Declares requirements for your mod. The version part of the string accepts a [semver](https://semver.org) or float version prefixed with an operator that describes which versions are required. The acceptable operators are

- `!` or `!=` for not equal to
- `=` or `==` for equal to
- `<` for less than
- `<=` for less than or equal to
- `>` for greater than
- `>=` for greater than or equal to

### Example
```squirrel
local mod = ::Hooks.register("mod_my_mod", "1.0.0", "My Cool Mod");
mod.require("mod_msu >= 1.2.0 (Modding Standards & Utilities)", "mod_EIMO [Because it's super great]", "mod_reforged < 0.3.0 (Reforged)");
// we require MSU versions greater than or equal to 1.2.0
// and if it is missing an error will print with the full
// Modding Standards & Utilities name
// we require any version of EIMO, if it missing 
// "Because it's super great" will be printed to the user
// we require versions of reforged smaller 0.3.0
// and in case of an error the full 'Reforged' name will be used
```

## Conflict With
```squirrel
<Mod>.conflictWith(...)
// ... is an arbitrary number of string parameters 
```

Each parameter is a string in the form `modID` `operator` `version` `(Friendly Mod Name)`, with everything except the modID being optional, and operator and version being required together.

Declares incompatibilities for your mod. The version part of the string accepts a [semver](https://semver.org) or float version prefixed with an operator that describes which versions are required. The acceptable operators are

- `!` or `!=` for not equal to
- `=` or `==` for equal to
- `<` for less than
- `<=` for less than or equal to
- `>` for greater than
- `>=` for greater than or equal to

The `Friendly Mod Name` here is completely redundant, but kept for the sake of symmetry between this and [[Mod Object#Require|require]]. This is because if the mod is present we can always check its self-reported 'friendly name'.

## Example
```squirrel
local mod = ::Hooks.register("mod_my_mod", "1.0.0", "My Cool Mod");
mod.conflictWith("mod_legends <16.0.0", "mod_autopilot");
// we conflict with legends versions less than 16
// we conflict with autopilot
```

# Queuing
## Queue a Function
```squirrel
<Mod>.queue( ..., _function, _bucket = null )
// ... is an arbitrary number of string parameters
// _function is a function
// _bucket is a value from the ::Hooks.BucketType table
```
each parameter in `...`  is a string in the form `<mod_id` or `>mod_id`. There can be 0
`_function` is the function that will be called later as determined by the queuing algorithm.
`_bucket` is the [[Queuing#Buckets|Bucket]] you'd like your function to be queued in, defaults to `::Hooks.BucketType.Normal`

This functions allows you to queue anything in `_function` relative to other mods, it is generally recommended that you queue the entirety of your mod in this way, even if you don't care about the load order of your mod, because other mods may depend on your mod in the future and wish to queue relative to it. Using `<` will load your mod before the modID specified and `>` will load your mod after

### Example
```squirrel
// First register your mod
local mod = ::Hooks.register("mod_my_mod", "1.0.0", "My Cool Mod!");
// then queue any other code you want to execute (hooks, registering JS files, ::includes, etc);
mod.queue(">mod_msu", "<mod_ends_buyback", function(){
	// load after MSU which as it is a modding library
	// load before Buyback because we modify the way prices for items are calculated (for example)
	// our actual hooks and any other code goes here
});

// if for some reason (for example because we are initializing classes) we want to use QueueBuckets we could do so here
mod.queue(">mod_msu", function(){
	// here we only care about queuing after MSU just in case
	// here we could instantiate BB Classes like
	local myItem = ::new("scripts/items/item");
	// because this is added to QueueBucket.AfterHooks by doing the following
}, ::Hooks.QueueBucket.AfterHooks)
```
