[[Hooks]] allow us to modify [[Battle Brothers Class|Battle Brothers Classes]] directly, you can imagine that this is happening during a sort of 'post complication' time, in practice this occurs while the game is booting up and reading all of the game scripts, before the main menu screen is shown. This allows us to 
- add functions
- wrap functions, having our code execute before, after or instead of the original code
- add fields
- set fields
of any bb class, without a runtime cost due to the hook running every time an object is instantiated. These changes are also inherited by descendants as though the class was defined with them in the first place. You can also perform arbitrary advanced operations using [[Raw Hooks]].

This method of modding is superior to [[Replacing Files|replacing files]] as it means mods will not conflict if both of them need to edit the same file. It is basically required to use hooks if you'd like to redistribute your mod for other people to enjoy.

## What is !mods_preload
If you've set up [[Environment Setup#BBbuilder|BBbuilder]], you should notice that it a file for you in `scripts/!mods_preload/`. This is the folder in which mods are able to register with Hooks, declare their dependencies, incompatibilities and load order relative to other mods.

Your file in this folder should be very small and it [[Best Practices#Main Mod File Name|follow the same name as your mod zip file]]. All it needs to 