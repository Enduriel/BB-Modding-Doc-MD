Hooks are targeted at specific [[Battle Brothers Classes]] and allow you to add or wrap functions and add or wrap fields within that class. A hook is a simple function that gets passed a special object which allows you to modify the targeted class. Since the BB Class is a table, you can manipulate this special object as though it was the BB Class table. By convention we use `q` to signify this special object.

## Hooking
```squirrel
<Mod>.hook( _src, _function )
// _src is a string
// _function is a function
```
`_src` is the file path of the [[Battle Brothers Class|BB Class]] we'd like to hook.
`_function` is the function that will get executed by the hook, it must have a single parameter: `q`.

This makes `_function` get called with a special object which allows for the addition and wrapping of functions, and addition and setting of fields. This can be done as though the object passed to `_function` was a normal table except in the case of wrapping functions, which requires the special syntax as seen in the example below.

## Example
```squirrel
local mod = ::Hooks.register("mod_my_mod", "1.0.0", "My Cool Mod!");
// first we must register our mod

// it is always recommended to queue hooks
// but for for this example we will avoid doing so
// for the sake of simplicity
mod.hook("scrippts/items/item", function(q) {
	// here we have targeted the base item bb class
	// we are now inside of the function that will get executed
	// with the special q object which allows us to modify
	// the item bb class
	q.MyMod_foo <- function()
	{
		// we add a new function exactly the way we'd add a function
		// to any normal table
		// It is best practice to prefix your function name with
		// part of your mod ID to minimize naming collisions
		// (see best practices)
		::logInfo("foo!");
	}
	// now the function MyMod_foo exists in all items and we can call
	// MyMod_foo on any item to show this

	q.create = @(__original) function() {
		// we can wrap functions using this syntax
		// __original here is the original version of the function
		// we are hooking, before we apply our hook
		// this allows us to overwrite the function
		// while still calling the original code
		// and therefore maximize compatibility
		__original();
		this.MyMod_foo();
		::logInfo("create called!")
		// now every time an item is created both 'foo!'
		// and 'create called!' will be printed to log
	}

	q.addSprite = @(__native) function() {
		// if you're looking to hook a function only added by C++ functions,
		// you must instead use the __native parameter,
		// this will automatically create a realtime hook for you in onInit
	}

	q.foo = @() function() {
		// if a function (foo in this case) already exists in item
		// and we'd like to overwrite, we can just omit the __original
		// parameter and leave @() empty to signal we are overwriting
	}

	q.m.Name = "DEFAULT_NAME";
	// we can also set fields using this simple syntax
	
	q.m.MyMod_NewMember <- "NewValue";
	// and append new fields using the standard new slot syntax

	// do not try to use in to check
	// for the presence of functions or fields
	// this will not work with the q object,
	// (this is unfortunately a squirrel limitation)
	// instead use the provided contains function in both cases

	q.contains("getName") // true
	q.m.contains("NotPresentMember") // false
});
```

## Why Basic Hooks
Basic hooks are superior to raw hooks in that they perform additional error validation and fix a [bug with all prototypal hooks](https://discord.com/channels/965324395851694140/1052648104815513670). This makes it much easier to debug future issues and if other errors arise with hooks these can be fixed in basic hooks. I will also be looking to extend the functionality of basic hooks as the need arises.
