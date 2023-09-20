## Mod Zip file
Should be [snake_case](https://en.wikipedia.org/wiki/Snake_case), prefixed with `mod_`. For example
- `mod_msu.zip`
- `mod_modern_hooks.zip`
- `mod_eimo.zip`

It is also acceptable to append your version to the file name when sharing directly or uploading as a GitHub release, for example:
- `mod_msu_v1.2.0.zip`
- `mod_modern_hooks_v0.1.0.zip`

However, do keep in mind that [nexusmods](https://www.nexusmods.com/) will automatically append this information along with its own identifier for your mod file. When using nexusmods your mod files should end up looking like this when downloaded:
- `mod_dev_console-380-2-1-0-1681541885.zip`
- `mod_eimo-239-9-1-3-1679395150.zip`

## Main Mod File Name
The name of your main file in `scripts/!mods_preload` should be the same as the name of your [[#Mod Zip file|zip file]], except without any version information. For example:
- `scripts/!mods_preload/mod_msu.nut`
- `scripts/!mods_preload/mod_eimo.nut`

## Mod ID
This is used by other mods to recognize your mod (especially [[MSU]] and [[Introduction|Hooks]]) and should therefore never change, but it would ideally also be the same as your [[#Mod Zip file]], for example:
- `mod_msu`
- `mod_modern_hooks`

## Mod Version
[[Introduction|Modern Hooks]] enforces the use of [SemVer](https://semver.org/) for mods registering with it directly (though it does support float versions when patching registration for Adam's [Modding Script Hooks](https://www.nexusmods.com/battlebrothers/mods/42), as well as when setting version requirements to preserve backwards compatibility). For example:
- `1.0.0`
- `0.1.0`
- `1.4.12-beta.1+buildmetadata.wowlookathowfaryoucango.1.4.12312`

## Mod Structure
For this section, `mod_example_mod` is used a stand-in for the mod ID, your mod ID should follow the [[#Mod ID|appropriate convention]]. Mods should follow the following structure to prevent conflicts with other mods and allow for easy and clean organization of files. 

`/scripts/!mods_preload/mod_example_mod` This is the one main mod file every mod should have to register its presence. Follow the appropriate conventions for [[#Main Mod File Name|name]] and [[#Contents of Main Mod File|contents]].

`/mod_example_mod/` By using the global [[#include|::include]] function, it is possible to load additional scripts outside of the vanilla /scripts folder by including them from your main mod file. To be able to control the order of when your scripts are run, all hooks and any global (non-[[Battle Brothers Class|BB Class]])objects should be defined in this root folder. 

`ui/mods/mod_example_mod/` You should use this folder for your UI files in accordance with [[#JS/CSS Files|best practices for those files]].

`/scripts/items/example_mod_new_item`
`/scripts/skills/example_mod_new_skill`
If you have [[Battle Brothers Class|BB Classes]] inheriting from vanilla classes, those should follow the same organizational structure as vanilla, for example if you have an item that inherits from `scripts/items/item`, it should probably be in one of the subfolders in `scripts/items`, with the same logic following for skills, origins (called scenarios in-game) and any other BB Classes. Be sure to following the [[#New Battle Brothers Class Names|naming conventions]] for new BB classes

### ::include
The `::include` function allows you to load additional script files outside of the vanilla `/scripts` folder, this is often desirable as it allows you to control when your script are loaded. It can be combined with `::IO.enumerateFiles` to easily load an entire folder (including subdirectories) of files.

#### Example
We have a `/mod_example/` folder which contains the files `global_functions.nut` and `constants.nut`, as well another folder called `hooks/` which contains a number of files setting up [[Hooks||hooks]]. We'd like the `constants.nut` to be loaded first followed by `global_functions` and finally `hooks/`, but we don't care what order the hooks are loaded in relative to each other.

In our main file's [[Queuing]] function we would do the following
```squirrel
// inside queue function after registering with libraries like MSU
::include("mod_example/constants");
::include("mod_example/global_functions");
foreach (file in ::IO.enumerateFiles("mod_example/hooks"))
	::include(file);
```
and everything would get loaded in the order we wanted.

## Contents of Main Mod File
Your main mod file should declare a global table for your mod to use, with the keys
- ID: your [[#Mod ID]] (string)
- Name: a friendly name displayed to users (string)
- Version: a [SemVer](https://semver.org/) [[#Mod Version]] (string)

It should then register with [[Introduction|Modern Hooks]] (or [modding script hooks](https://www.nexusmods.com/battlebrothers/mods/42) for older mods), and [[Queuing]] the main mod function that will be executed for your mod. The first thing you should do when queuing is register with any libraries (like [[MSU]]) that you use. Finally, if your mod is any larger than one or two hooks, you should [[#include|::include]] files for your mod, and register and JS/CSS file your mod adds.

### Example
```squirrel
::MyCoolMod <- {
	ID = "mod_my_cool_mod",
	Name = "My AwesomeSauce Mod",
	Version = "1.0.1-beta.1"
}

local mod = ::Hooks.register(::MyCoolMod.ID, ::MyCoolMod.Name, ::MyCoolMod.Version);

mod.declareCompatibilityData({
	Requirements = {
		mod_i_need = {Version = ">=1.14.4"},
		mod_i_need_and_want_to_load_before = {}
	},
	Incompatibilities = {
		mod_that_breaks_on_specific_version = {Version = "1.4.0"}
	}
});
mod.queueFunction({
	Before = ["mod_i_need_and_want_to_load_before"],
	After = ["mod_i_want_to_load_after"]
}, function() {
	::MyCoolMod.Mod <- ::MSU.Class.Mod(::MyCoolMod.ID, ::MyCoolMod.Version, ::MyCoolMod.Name) // registering with MSU
	::include("mod_my_cool_mod/load"); // running loading script in main folder
	::Hooks.registerJS("ui/mods/my_cool_mod/my_screen.js"); // registering JS file
	::Hooks.registerCSS("ui/mods/my_cool_mod/css/my_css.css"); // registering CSS file
});
```

## New Class Fields
When adding a new field to a [[Battle Brothers Class]], your field should begin with your mod name in [PascalCase](https://techterms.com/definition/pascalcase)., followed by an underscore `_`, followed by the name of your new field in [PascalCase](https://techterms.com/definition/pascalcase). For example:
- `MyCoolMod_MyNewField`
- `MyOtherMod_MySpecialField`

This is to prevent collisions caused by two mods adding the same field to the same class or an ancestor/descendant.
## New Class functions
When adding a new function to a [[Battle Brothers Class]], your function should begin with your mod name in [PascalCase](https://techterms.com/definition/pascalcase)., followed by an underscore `_`, followed by the name of your new field in [camelCase](https://techterms.com/definition/camelcase). For example:
- `MyCoolMod_myNewFunction`
- `MyOtherMod_mySpecialFunction`

This is to prevent collisions caused by two mods adding the same function to the same class or an ancestor/descendant.
## Function parameters
In all of Battle Brothers, function parameter named begin with an underscore `_` followed by the name of the parameter in [camelCase](https://techterms.com/definition/camelcase), it is therefore strongly recommended to follow the same convention. For example
- `_someParameter`
- `_someOtherVeryLongParamater`
- `_parameter`

### New Battle Brothers Class Names
When adding new [[Battle Brothers Class|Battle Brothers Classes]] to an existing folder, prefix your file name with your [[#Mod ID]], except in this case you do not need to begin with `mod_`. For example:
- `scripts/items/weapons/my_cool_mod_my_cool_weapon.nut`
- `scripts/skills/actives/my_cool_mod_my_new_skill.nut`

## JS/CSS Files
When adding new JS or CSS files, you should use a new folder in `ui/mods/` for all of your mods UI content. This folder should have the same name as your mod id, though the `mod_` prefix is optional. For example:
- `ui/mods/mod_my_cool_mod`
- `ui/mods/my_other_mod`

Then, you would place your JS/CSS files inside that folder however you wish, though it is generally recommended to group your css together, you might therefore end up with files something like:
- `ui/mods/mod_my_cool_mod/css/my_special_css.css`
- `ui/mods/my_other_mod/js/my_new_screen.js`


