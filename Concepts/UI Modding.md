As described on the [[Home#Engine & Programming Languages|home page]], Battle Brothers effectively uses a web browser to display all the UI elements that are shown to the player. Due to the old version of the Chromium browser bundled with the game (Chromium 48), the game only supports ES3 JavaScript, which was released in 1999. This means that using modern JavaScript programming techniques is quite difficult. Although KFox is currently working on [adding functionality to BBbuilder](https://github.com/TaroEld/BBbuilder/pull/5) which would allow for usage of modern JavaScript.

The game basically just loads the ui/main.html file from data_001.dat, which in turn loads a bunch of CSS and JS files (including [jQuery](https://jquery.com/), which is the main framework the game uses).
## Screens
Files are generally organized into 'screens' which you can imagine as one unified portion of the UI. For example, there is a 'main menu screen', a 'character screen', a 'world screen', etc. Each screen will generally have a JS file and a CSS file associated with that screen. Some screens also have [[#Modules]].

Each screen will generally have a respective [[Battle Brothers Class|BB Class]], with the same file name. These bb classes will connect to their frontend equivalents, and both sides are able to call each others functions with arguments, which is how data is transferred between the frontend and backend. When calling a JS function from squirrel, you can only do so asynchronously and cannot handle the result of the JS function. However, when calling Squirrel functions from JS you can also pass a callback that will be called with the return value of the squirrel function being called by JS. 
## Modules
Modules are sort of like subscreens, they do not have a directly squirrel side counterpart, and instead rely on the main screen (or a datasource) for communication with the game's logic.

For example the main menu screen has 
- a main menu module
- a load campaign module
- a new campaign module
- a scenario menu module
- a options menu module
- a credits module

Screens with modules are somewhat uncommon and more complex that basic screens, and generally when modding new screens yourself you do not need to bother with modules.

## Adding Files
To make it so that mods don't need to overwrite UI files, [[Modern Hooks/UI Modding|modern hooks]] allows us to register JS and CSS files to load after all the vanilla files have been loaded, but before all of the screens have been instantiated (this is different to when modding script hooks loaded UI files, which was just after the main menu screen was shown to the player).



TODO (lots lol)