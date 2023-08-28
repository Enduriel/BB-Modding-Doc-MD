[[Introduction#Registering your mod|::Hooks.register]] returns an instance of Modern Hooks' Mod [Squirrel Class](https://developer.electricimp.com/squirrel/squirrel-guide/classes). This allows you to

## Declare Compatibility Data
```squirrel
<Mod>.declareCompatibilityData( _compatibilityData );
// _compatibilityData is a table
```
`_compatibilityData` is a table with 2 optional keys (`Requirements` and `Incompatibilities`), each of which should be a table with the keys being mod IDs and the values being a table with optional additional information about the compatibility (currently this can only be the version that it applies to)

Declares the mods your mod is incompatible with or requires. The Version key in a specific compatibility accepts a [semver](https://semver.org/) or float version prefixed with an operator that describes which versions should be included in the filter. The acceptable prefixes are:
- `!` or `!=` for not equal to
- `=` or `==` for equal to
- `<` for less than
- `<=` for less than or equal to
- `>` for greater than
- `>=` for greater than or equal to

### Example
```squirrel
local mymod = ::Hooks.register("mod_my_mod", "1.0.0", "My Cool Mod");
mymod.declareCompatibilityData({
	Requirements = {
		mod_msu = {} // require any version of MSU
		mod_swifter = {Version = ">=1.1.0"} // require swifter versions greater than or equal to 1.1.0
	}
	Incompatibilities = {
		mod_legends = {Version = "<16.0.0"} // incompatible with legends versions less than 16.0.0
		mod_clever_recruiter = {Version = "=1.0.1"} // incompatible with clever recruiter version 1.0.1
		mod_hooks = {Version = "<=20.0"} // incompatible with mod_hooks versions equal to or less than 20.0
	}
})
```

## Queue a Function
```squirrel
<Mod>.queueFunction( _loadOrderData, _function, _bucket = null )
// _loadOrderData is a table but can be null
// _function is a function
// _bucket is a value from the ::Hooks.BucketType table
```
`_loadOrderData` is a table with 2 *optional* keys (`After` and `Before`), each of which should contain arrays of string mod IDs as elements.
`_function` is the function that will be called later as determined by the queuing algorithm.
`_bucket` is the [[Queuing#Buckets|Bucket]] you'd like your function to be queued in, defaults to `::Hooks.BucketType.Normal`

This functions allows you to queue anything in `_function` relative to other mods, it is generally recommended that you queue the entirety of your mod in this way, even if you don't care about the load order of your mod, because other mods may depend on your mod in the future and wish to queue relative to it.

### Example
```squirrel
// First register your mod
local myMod = ::Hooks.register("mod_my_mod", "1.0.0", "My Cool Mod!");
// then queue any other code you want to execute (hooks, registering JS files, ::includes, etc);
myMod.queueFunction({
	After = ["mod_msu" /*load after MSU which as it is a modding library*/]
	Before = ["mod_ends_buyback", /*load before Buyback because we modify the way prices for items are calculated (for example)*/
		"mod_example_mod"]
}, function(){
	// our actual hooks and any other code goes here
})
// if for some reason (for example because we are initializing classes) we want to use QueueBuckets we could do so here
myMod.queueFunction({
	After = ["mod_msu"] // here we only care about queueing after MSU just in case
}, function(){
	// here we could instantiate BB Classes like
	local myVec = ::vec2f(0,0);
	// because this is added to QueueBucket.AfterHooks by doing the following
}, ::Hooks.QueueBucket.AfterHooks);
```