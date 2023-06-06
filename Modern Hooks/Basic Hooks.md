## Adding Functions
```squirrel
::Hooks.addFunctions( _modID, _scriptPath, _newFunctions )
// _modID and _scriptPath are strings
// _newFunctions is a table
```
`_modID` is the ID of your mod, allowing for easier identification of hooks for debugging purposes.
`_scriptPath` is the path of the file you're trying to hook
`_newFunctions` is a table of all the functions you want to add to the class

Adds a table of functions to the class with the given `_scriptPath`. Requires that the functions you're trying to add are actually new, it will print warnings to log if they're not. If you're trying to overwrite an already existing function you should use [[#Wrapping Functions|wrapFunctions]].

### Example
If you want to add a new `foo` function to all items, you could do this using modern hooks
```squirrel
::Hooks.addFunctions("mod_my_cool_mod", "scripts/items/item", {
	function foo()
	{
		return "wow look at how easy that was";
	}
});
```
You can add more functions by simply adding them to the `_newFunctions` table:
```squirrel
::Hooks.addFunctions("mod_my_cool_mod", "scripts/items/item", {
	function foo()
	{
		return "wow look at how easy that was";
	}
	function bar()
	{
		return "wow this is easy too";
	}
});
```

### Best Practices
To minimize function name clashes when adding new functions, since all newly added functions are usable by all mods, it is recommended to prefix your new function names with your mod name in [PascalCase](https://techterms.com/definition/pascalcase). To follow this convention we would instead do:
```squirrel
::Hooks.addFunctions("mod_my_cool_mod", "scripts/items/item", {
	function MyCoolMod_foo()
	{
		return "wow look at how easy that was";
	}
	function MyCoolMod_bar()
	{
		return "wow this is easy too";
	}
});
```
If this isn't followed it is possible that two mods will add a new function with the same function name that does something slightly (or completely) different, which will cause conflicts.


## Wrapping Functions
```squirrel
::Hooks.wrapFunctions( _modID, _scriptPath, _functionWrappers )
// _modID and _scriptPath are strings
// _functionWrappers is a table
```
`_modID` is the ID of your mod, allowing for easier identification of hooks for debugging purposes.
`_scriptPath` is the path of the file you're trying to hook
`_functionWrappers` is a table containing wrappers for the functions you're trying to wrap.

Wraps all the functions in the `_functionWrappers` table. Requires that the functions you're trying to wrap already exist, and will print warnings to log if they don't. If you're trying to add new functions you should use [[#Adding Functions|addFunctions]].

### Example
If you want to add a "Hello World" to the end of the name of items, you could do this by wrapping the `getName` function in item and adding `"Hello World"` to whatever it returns. Wrapping is a bit weirder then adding functions so it's best to just following the example:
```squirrel
::Hooks.wrapFunctions("mod_my_cool_mod", "scripts/items/item", {
	function getName( _originalFunction ) // this has the name of the function we're wrapping and has the function being wrapped as a parameter
	{
		return function() // here we return the new function that will replace the original, if the original function has parameters we must have the same parameters here 
		{
			return _originalFunction() + " Hello World";
		}
	}
});
```
Just like with `addFunctions` if you'd like to wrap multiple functions at a time you can add them to the `_functionWrappers` table like so:
```squirrel
::Hooks.wrapFunctions("mod_my_cool_mod", "scripts/items/item", {
	function getName( _originalFunction )
	{
		return function()
		{
			return _originalFunction() + " Hello World";
		}
	}
	function drop( _originalFunction )
	{
		return function( _tile = null ) // copying the parameters from the item drop function
		{
			::logInfo("An Item was dropped!"); // print to log that an item was dropped
			return _originalFunction(_tile) // run the original function and return its return value
		}
	}
});
```


## Adding Fields
```squirrel
::Hooks.addFields( _modID, _scriptPath, _fieldsToAdd )
// _modID and _scriptPath are strings
// _fieldsToAdd is a table
```
`_modID` is the ID of your mod, allowing for easier identification of hooks for debugging purposes.
`_scriptPath` is the path of the file you're trying to hook
`_fieldsToAdd` is a table containing the fields you'd like to add and the default value to set them to.

Attempts to add the specified `_fieldsToAdd` to the m table of the bb class at `_scriptPath`. It checks the ancestors of the class and the class itself and will print a warning if trying to add a field that already exists anywhere in the inheritance chain. If you're trying to set the value of a field that already exists, you should instead use [[#Setting Fields|setFields]].

### Example
If you want to add a `MyField` field to the m table of all items, you would simply do this by calling addFields with your specified field name and value
```squirrel
::Hooks.addFields("mod_my_cool_mod", "scripts/items/item", {
	MyField = "EZ"
});
```
now *any* item will have a `MyField` field in its `m` table. Adding multiple fields simultaneously can be done by passing a larger table:
```squirrel
::Hooks.addFields("mod_my_cool_mod", "scripts/items/item", {
	MyField = "EZ",
	MyOtherField = 124,
	MyFinalField = false
});
```
### Best Practices
As with functions, to minimize field name clashes it is recommended to prefix your field names with your mod name in [PascalCase](https://techterms.com/definition/pascalcase). To follow this convention we would instead do:
```squirrel
::Hooks.addFields("mod_my_cool_mod", "scripts/items/item", {
	MyCoolMod_MyField = "EZ",
	MyCoolMod_MyOtherField = 124,
	MyCoolMod_MyFinalField = false
});

```


## Setting Fields
```squirrel
::Hooks.setFields( _modID, _scriptPath, _fieldsToSet )
// _modID and _scriptPath are strings
// _fieldsToSet is a table
```
`_modID` is the ID of your mod, allowing for easier identification of hooks for debugging purposes.
`_scriptPath` is the path of the file you're trying to hook
`_fieldsToSet` is a table containing the fields you'd like to set and the value to set them to.

Attempts to set fields to the associated values. It follow the chain of inheritance to set the fields in ancestors if necessary, and it will never create a new field to set. If the field doesn't the function will print a warning. If you're trying to add new fields you should instead use [[#Adding Fields|addFields]].

### Example
If you want to set the default name for items that don't have their name set to `"ERROR FIX ME"` instead of the empty string, you could do:
```squirrel
::Hooks.setFields("mod_my_cool_mod", "scripts/items/item", {
	Name = "ERROR FIX ME"
});
```
now any item that doesn't have a `this.Name = "XYZ"` line in its create function will default to the name above. If you want to set multiple fields at the same time you can do the following:
```squirrel
::Hooks.setFields("mod_my_cool_mod", "scripts/items/item", {
	Name = "ERROR FIX ME",
	ID = "ERROR UNSET ID"
});
```