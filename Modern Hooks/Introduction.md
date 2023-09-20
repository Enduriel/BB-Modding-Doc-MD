### Links:
Github
NexusMods

[[Hooks]] allow us to modify [[Battle Brothers Class|Battle Brothers Classes]] directly, this happens during initialization time, while the game is booting up and reading all of the game scripts and before the main menu screen is shown. This allows us to 
- [[Basic Hooks#Adding Functions|add functions]]
- [[Basic Hooks#Wrapping Functions|wrap functions]], having our code execute before, after or instead of the original code
- [[Basic Hooks#Adding Fields|add fields]]
- [[Basic Hooks#Setting Fields|set fields]]

of any bb class, without a runtime cost due to the hook running every time an object is instantiated. These changes are also inherited by descendants as though the class was defined with them in the first place. You can also perform arbitrary advanced operations using [[Raw Hooks]].

This method of modding is superior to [[Replacing Files|replacing files]] as it means mods will not conflict if both of them need to edit the same file. It is basically required to use hooks if you'd like to redistribute your mod for other people to enjoy. 

## What is !mods_preload
If you've set up [[Environment Setup#BBbuilder|BBbuilder]], you should notice that it adds a file for you in `scripts/!mods_preload/`. This is the folder in which mods are able to register with [[Introduction|Modern Hooks]], declare their dependencies, incompatibilities and load order relative to other mods.

Your file in this folder should be very small and [[Best Practices#Main Mod File Name|have the same name as your mod zip file]]. All it needs to do is [[#Registering your mod|register with Modern Hooks]], [[Mod Object#Requirement and Incompatibilities|declare requirements and incompatibilities]] and [[Mod Object#Queuing a Function|queue your mods main function]], your main function should be short and just [[Best Practices#include|::include]] files for your mod, which is where your actual logic should be. This allows for much better [[Best Practices#Mod Structure|mod structure]].

## Registering your mod
```squirrel
::Hooks.register( _modID, _modVersion, _modName, _metaData = null)
// _modID, _modVersion and _modName are strings
// _metaData is a table
```
`_modID` is the [[Best Practices#Mod ID|ID]] that Modern Hooks and other mods will use to identify your mod. This should never change after you release a public version of your mod. The ID can only consist of the characters `a-z`, `A-Z`, `0-9` and `_`.
`_modVersion` is the [[Best Practices#Mod Version|SemVer Version]] of your mod.
`_modName` is an end-user friendly name for your mod. This should be identifiable to the mod's end users, and will be displayed along with errors or warning detected by Modern Hooks related to your mod.
`_metaData` allows you to store any arbitrary data with your mod objects for other mods to use.

This function registers your mod with Modern Hooks, allowing you to hook [[Battle Brothers Class|BB Classes]] using your mod's ID and allowing other mods to declare incompatibility with your mod or set your mod as a requirement.
Returns a [[Mod Object]] which allows you to declare incompatibilities, requirements and queue your functions.

### Example
```squirrel
local myMod = ::Hooks.register("mod_my_mod", "1.0.0", "My Cool Mod!");
```