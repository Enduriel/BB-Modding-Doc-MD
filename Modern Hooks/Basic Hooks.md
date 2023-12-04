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

	// you can also delete existing slots using delete
	delete q.MyMod_foo;

	// and iterate over functions or fields as normal
	foreach (funcName, func in q)
		::logInfo(format("Function Name: %s, Function Address: %s", funcName, func.tostring()))

	foreach (fieldName, field in q.m)
		::logInfo(format("Field Name: %s, Field Value: %s", fieldName, field + ""))
});
```

## Why Basic Hooks
Basic hooks are superior to raw hooks in that they perform additional error validation and fix a [bug with all prototypal hooks](https://discord.com/channels/965324395851694140/1052648104815513670). This makes it much easier to debug future issues and if other errors arise with hooks these can be fixed in basic hooks. I will also be looking to extend the functionality of basic hooks as the need arises.

## Why the @
You may have noticed that the way you set an existing function to a new function is strange in Modern Hooks, instead of directly setting the function there's this `@()` or `@(__original)` in between the `=` and the function definition.
This may seem a bit mystical, but it is actually just a [lambda function](https://developer.electricimp.com/squirrel/squirrel-guide/functions#lambda-functions). Therefore 
```squirrel
q.foo = @(__original) function() {
	__original()
	// do something
}
```
is equivalent to
```squirrel
q.foo = function(__original) {
	return function() {
		__original()
		// do something
	}
}
```
So the lambda there saves us a couple lines and a level of indentation, but this doesn't really answer why we need a function that creates our function in the first place. To explain that, it helps to first look at the way this would have been done before (and is still how you would have to do this in [[Raw Hooks]]):
```squirrel
local oldfoo = o.foo;
o.foo = function(){
	oldfoo();
	// do something
}
```
There are a few problems with the above approach.
- The local variable doesn't have a defined name which can cause issues when collaborating with others with a different style (`foo` vs `oldFoo` vs `ogFoo` vs `originalFoo` vs etc).
 - If this is done during a hook which runs while the game is loading files (anything except `hookNewObject`), this will error if `foo` only exists in an ancestor and not in the target class.
 - If you use something more advanced to get around the previous issue (for example `::mods_getMember`), your code may have a [very strange bug](https://discord.com/channels/965324395851694140/1052648104815513670) which occurs in some edge cases.
 - If the function doesn't exist in `o`, (but does in an ancestor) this will error, which can be annoying.

By using the wrapper, we are able to get around all of the above issues, your code only receives the original function, regardless of whether it's in this class or an ancestor. If it is an ancestor which would cause the aforementioned bug, a patch is automatically applied to `__original` which will fix it.