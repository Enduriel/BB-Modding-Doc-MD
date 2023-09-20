After reading [[Concepts/UI Modding|UI Modding]], you may be interested in using Modern Hooks to load your JS or CSS files, fortunately, this is very simple, with one function for each filetype. These files are executed after all vanilla files are read and [[Concepts/UI Modding#Screens|Screens]] are defined, but before any of them (except for Root Screen due to engine limitations) are instantiated.

## registerJS
```squirrel
::Hooks.registerJS( _filePath )
// _filePath is a string
```
`_filePath` is the full string path of wherever the UI file you'd like to load is, due to engine limitations, this must always begin with `ui/`.

Adds a [JavaScript](https://developer.mozilla.org/en-US/docs/Learn/JavaScript) file at the specified `_filePath` to be loaded during game initialization.

### Example
If we have a file `ui/mods/mod_example_mod/my_new_screen.js`, as we are following [[Best Practices#JS/CSS Files|best practices]] for organizing JS files, we could include it by simply doing the following in our [[Queuing|queued function]]:
```squirrel
::Hooks.registerJS("ui/mods/mod_example_mod/my_new_screen.js");
```

## registerLateJS
```squirrel
::Hooks.registerLateJS( _filePath )
// _filePath is a string
```
`_filePath` is the full string path of wherever the UI file you'd like to load is, due to engine limitations, this must always begin with `ui/`.

Adds a [JavaScript](https://developer.mozilla.org/en-US/docs/Learn/JavaScript) file at the specified `_filePath` to be loaded during game initialization, but only after vanilla screens are already instantiated.
## registerCSS
```squirrel
::Hooks.registerCSS( _filePath )
// _filePath is a string
```
`_filePath` is the full string path of wherever the UI file you'd like to load is, due to engine limitations, this must always begin with `ui/`.

Adds a [CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS) file at the specified `_filePath` to be loaded during game initialization.
### Example
If we have a file `ui/mods/mod_example_mod/css/my_css.css`, as we are following [[Best Practices#JS/CSS Files|best practices]] for organizing CSS files, we could include it by simply doing the following in our [[Queuing|queued function]]:
```squirrel
::Hooks.registerCSS("ui/mods/mod_example_mod/css/my_css.css");
```


## Note for Veteran Modders
Modern Hooks' `::Hooks.registerJS`/`::Hooks.registerCSS` function are very similar to Modding Script Hooks' `::mods_registerJS`/`::mods_registerCSS` functions, but they are not directly transferable.

Modding Script Hooks' load files after the main menu screen is Initialized Squirrel side, this is very late in launch process, effectively simultaneously to the main menu screen being shown to the player. This means that a lot of hooks, especially for functions like `registerDatasourceListener` which would be executed immediately upon instantiation, would never run when using Modding Script Hooks' hooks, requiring special work arounds.

Modern Hooks work around this completely by executing hooks much earlier, however this means that *if* your mod had any such workarounds, they will not be directly transferable to Modern Hooks and should be removed before switching your files over to using [[#registerJS]] or [[#registerCSS]].