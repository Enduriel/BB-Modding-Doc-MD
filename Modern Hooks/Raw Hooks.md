Modern hooks allows experienced modders to directly modify the [[Battle Brothers Class|prototype object]] being hooked. This is how all hooks used to be written for [Modding Script Hooks](https://www.nexusmods.com/battlebrothers/mods/42), and both [[Basic Hooks|basic hooks]] and [[Leaf Hooks|leaf hooks]] ultimately create raw hooks that are run on the prototype object. ***However, those specialized hooks perform some additional error handling, validation, and even fix some edge case bugs, and should therefore be used in place of raw hooks whenever possible.***

## Example
The basic idea around raw hooks is very simple, you register a function that you'd like to be called when a specific prototype object is processed, and then your function is executed with the prototype as the sole passed argument.
```squirrel
::Hooks.rawHook("mod_my_cool_mod", "scripts/items/item", function(_prototype) {
	// _prototype here is the prototype BB Class
	// we can set the default value for a field
	_prototype.m.Name = "ERROR UNSET NAME";
	// add a function
	_prototype.MyCoolMod_foo <- function()
	{
		::logInfo("Hello World!");
	}
	// or perform any other manipulation
});
```
An identically structured function exists for the leaf variant of raw hooks, which is used by all leaf hooks.
```squirrel
::Hooks.rawLeafHook("mod_my_cool_mod", "scripts/items/item", function(_prototype) {
	// this will execute once for each item
	// and each time _prototype will be the BB Class
	// for the item being hooked
});
```
