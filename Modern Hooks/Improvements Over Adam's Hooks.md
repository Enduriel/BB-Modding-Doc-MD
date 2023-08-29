Adam's creation of [modding script hooks](https://www.nexusmods.com/battlebrothers/mods/42) revolutionized Battle Brothers modding. Before it existed we were unable to mod Battle Brothers except by overwriting files or changes configs. Unfortunately, after years of using and relying on it for all of my mods, I've noticed a lot of ways in which it catches out new and experienced modders alike, causing them to write potentially buggy code and find it difficult to understand exactly what they're doing.  
  
The MSU team had been bouncing around the idea of MSU hooks for a while but we weren't able to cleanly overcome one specific issue (a standard way to hook ALL classes with 1 hook) until I discovered a method to do so in May. With Midas and Taro mostly unavailable I took on the challenge of writing everything up myself.

## Squirrel Hooks

### Raw Hooks
***IMPORTANT NOTE***
```
This is an example only you shouldn't use Modern Hooks like this, refer to the section on the new Basic Hooks below
```
All of the 'raw' hooks in modding script hooks have been turned into a *single* hook in Modern Hooks. This means that instead of
- `::mod_hookClass`
- `::mod_hookExactClass`
- `::mod_hookNewObject`
- `::mod_hookNewObjectOnce`
- `::mod_hookBaseClass`

we now only have [[Raw Hooks|::Hooks.rawHook]], which is actually a drop in replacement for `::mod_hookExactClass` (and actually patches hookExactClass to redirect to `::Hooks.rawHook`), except it is able to apply to ALL Battle Brothers Classes, with no exceptions. However, we should instead use the new [[#New Basic Hooks|basic hooks]].

### Hooking Descendants
***IMPORTANT NOTE***
```
This is an example only you shouldn't use Modern Hooks like this, refer to the section on the new Basic Hooks below
```
Additionally, `mod_hookDescendants` is superseded by [[Raw Hooks|Hooks.rawLeafHook]]. The distinction is easiest to explain with an example. Let's hook `scripts/items/item` to add a new function `foo` to it which prints `foo` to the log, then let's use hooDescendants to wrap that call and print `bar`.
```squirrel
::mods_hookBaseClass("items/item", function(o) {
	o.foo <- function() {
		::logInfo("foo");
	}
});
::mods_hookDescedants("items/item", function (o) {
	local foo = o.foo;
	o.foo = function() {
		foo();
		::logInfo("bar");
	}
});
```
Now, how many items would `bar` print to the log if you called `foo()` on a given item? In the example above it will print once for every layer of inheritance between `scripts/items/item` and the item you are calling `foo()` on. For example, for `scripts/items/weapons/weapon` `bar` would print once, for `scripts/items/weapons/sword` it would print twice. If you instantiated `scripts/items/item` and called `foo()` on it, `bar` would never print to the log. This is confusing, usually drains performance, and in my experience has never once been a useful feature, in comparison `::Hooks.rawLeafHook` prints would print `bar` exactly once for any descendant of item and item itself. Here is the same code using modern hooks:
```squirrel
// this is an example only you should almost never be using raw hooks in modern hooks
::Hooks.rawHook("mod_my_mod", "scripts/items/item", function(p){
	p.foo <- function() {
		::logInfo("foo");
	}
});
::Hooks.rawLeafHook("mod_my_mod", "scripts/items/item", function(p){
	local foo = p.foo;
	p.foo = function() {
		foo();
		::logInfo("bar");
	}
});
```
This would print `bar` exactly once when calling `foo()` on any item, including item itself.

### New Basic Hooks
Instead of using raw style hooks, when using Modern Hooks you should instead be using [[Basic Hooks]] for 99%+ of your hooks. These also:
- Warn if a mod tries to add a function/field to a class which already exists in the class or an ancestor
- Warn if a mod tries to set a field or wrap a function which don't exist 
- Warn if a mod wraps a function with a different number of parameters than the function the wrapper returns.
- Fix a very prevalent bug when wrapping functions of grandparents of the target class. [Discord Thread](https://discord.com/channels/965324395851694140/1052648104815513670) on the subject (credit to LordMidas for discovering it) in the [Modding Discord](https://discord.gg/HFrdY9aGBk)
- Will likely have additional features added to them to help with finding mod conflicts and other issues.

There are 4 normal types and 4 equivalent 'leaf' types, with no overlap between them, and the use-case for each should be fairly clear:
- `addNewFunctions` add functions to the target class
	- `addNewLeafFunctions` exists, though I don't really see a use-case
- `wrapFunctions` wrap existing functions
	- `wrapLeafFunctions` wrap existing functions for all descendants and the target class
- `addFields` add fields to the target class
	- `addLeafFields` exists, though I don't really see a use-case,
- `setFields` set existing fields in the target class to a new default in the target class.
	- `setLeafFields` set existing fields to a new default for all descendants and the target class.

## UI/JavaScript

### EarlyJS
Adam's Hooks run the JS files registed with it very late, only after ingame screens are instantiated and the main menu is shown, this can be problematic when trying to hook the main menu or functions that are called during any screen's instantiation, the most common example being `createModules` and `registerDatasourceListener`. In Adam's hooks these cases require special handling where you directly modify the screens rather than hooking those functions.

Modern Hooks instead run [[Modern Hooks/UI Modding#registerJS|registered JS]] and [[Modern Hooks/UI Modding#registerCSS|CSS]] files after all the vanilla files are read and screens are defined, but *before* screens are instantiated. This means that it is possible to hook all functions normally, and even the screens themselves directly. In some edge cases you might still want to run JS code after screens are instantiated, in which case Modern Hooks provides [[Modern Hooks/UI Modding#registerLateJS|registerLateJS]] to achieve that goal.

## Dependencies and Queueing

### Semantic Versions
Modern hooks adds support for (and in fact requires) [semantic versions](https://semver.org). Previously, MSU was patching Adam's Hooks to add support for it, now you can use semver even in small mods that don't require MSU.
### Dependencies
Dependencies and queuing were an addon to Adam's hooks and therefore the implementation ended up being a little strange with them being handled together despite being separate concepts. Some of these strange behaviors include:
- It is impossible to require another mod and queue before it (though MSU does patch this out)
- You can split up your mod requirements across multiple queued functions and they all apply.
- It is impossible to queue one part of your mod before a mod and a different part after.

In Modern Hooks, [[Requirements and Incompatibilities]] are handled separately from mod queueing, which makes a lot more logical sense. Additionally it's also possible to add the name (not just the ID) of the mod you depend on so that users get a more user-friendly error message.

### Queueing

#### Function focused Queuing algorithm
Modern Hooks uses a new queueing algorithm which allows it to handle situations like queuing two separate parts of your mod at different positions relative to another mod. For example, you could queue one part of your mod before MSU, and another after. This isn't possible with Adam's Hooks.
#### Queue Buckets
[[Queuing#Buckets|Buckets]] are a new concept allowing specialized mods to queue certain functions earlier or later than all other mods. This is mostly useful for Modern Hooks itself, modding libraries like MSU, and larger mods like Legends or Reforged. MSU for example had its own [endqueue system](https://github.com/MSUTeam/MSU/blob/49177ceb4ce3dca8ece18f48d39e66c3c383896b/scripts/!mods_preload/msu.nut) to try and force certain hooks to run after the normal queueing system. Additionally...

#### AfterHooks
The [[Queuing#AfterHooks|AfterHooks QueueBucket]] allows you to queue code to run after all hooks have completed, this is important as you should not be instantiating squirrel or native objects before all hooks and normal functions have run, as if you do they will fail. This bucket allows you to easily do that.x`