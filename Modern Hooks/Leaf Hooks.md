Sometimes when hooking, you want to wrap a specific function for *all* descendants of a specific object, regardless of how many times they are defined in the descendants of that function.

As an example let us take the `getBuyPrice` function of items. Let's say we'd like to double the buy price of all items, regardless of any other context. Without leaf hooks this would be very difficult to do, as this function is not only defined in item, but also has custom implementations in growth_pearls_item, sergeant_badge_item, ancient_amber_item and a multitude of other items. This would mean that we'd need to use a `::Hooks.wrapFunctions` function targeting each of those items in each of those cases. 

Fortunately, due to the fact that inheritance *clones* the parent prototype object during inheritance (rather than instantiation), we are able to wait until all inheritance is completed, and only then apply our 'leaf hooks' to the bb class as well as all descendants of the bb class we are targeting our hook at. This guarantees that our wrapper is applied exactly once to each descendant of the target class, and also that it applies *after* all other inheritance.

To use this 'leaf hook' system, each of the hooking functions has a leaf style counterpart:
- wrapLeafFunctions
- addLeafFunctions
- setLeafFunctions
- addLeafFunctions

These act exactly the same as the normal hooks, except that they are applied to all descendants of the target class (as well as the target class) after all inheritance. I actually don't see a use case for the functions other than `wrapLeafFunctions`, but they have been added for the sake of completeness and perhaps someone else will find some way to use them. I would not recommend using the other functions unless you *really* know what you're doing.

Finally, to double the purchase price of all items, you would simply do:
```squirrel
::Hooks.wrapLeafFunctions("mod_my_cool_mod", "scripts/items/item" {
	function getBuyPrice(_originalFunction)
	{
		return function() {
			return _originalFunction() * 2;
		}
	}
});
```