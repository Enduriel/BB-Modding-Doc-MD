Sometimes when [[Basic Hooks|hooking]], you want to wrap a specific function for *all* descendants of a specific object, regardless of how many times they are defined in the descendants of that function.

As an example let us take the `getBuyPrice` function of items. Let's say we'd like to double the buy price of all items, regardless of any other context. Without leaf hooks this would be very difficult to do, as this function is not only defined in `scripts/items/item`, but also has custom implementations in `scripts/items/loot/growth_pearls_item`, `scripts/items/accessory/sergeant_badge_item`, `scripts/items/loot/ancient_amber_item` and a multitude of other items. This would mean that we'd need to use a basic hook targeting each of those custom implementation to achieve our goal.

Fortunately, due to the fact that inheritance *clones* the parent prototype object during inheritance (rather than instantiation), we are able to wait until all inheritance is completed, and only then apply our 'leaf hooks' to the bb class as well as all descendants of the bb class we are targeting our hook at. This guarantees that our wrapper is applied exactly once to each descendant of the target class, and also that it applies *after* all other inheritance.

To use this 'leaf hook' system, we instead use the `leafHook` function in the same way we would the normal [[Basic Hooks|hook]] function:

## Example
```squirrel
local mod = ::Hooks.register("mod_my_mod", "1.0.0", "My Cool Mod!");
// first we must register our mod

// it is always recommended to queue hooks
// but for for this example we will avoid doing so
// for the sake of simplicity
mod.leafHook("scrippts/items/item", function(q){
	// this is now executing once for item
	// and every descendant of item
	// but since this is done after inheritance takes place,
	// we do not need to worry about hooking the same object
	// multiple times

	q.getBuyPrice = @(__original) function() {
		return __original() * 2;
		// we can perform this simple wrap which will guarantee
		// that all items have their buy price doubled
		// in all circumstances;
		// of course you could add more complex logic here
	}
});
```