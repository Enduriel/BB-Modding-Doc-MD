Vanilla data is loaded exactly the same way as mod data. In your battle brothers install you will notice a /data folder inside of which you'll have a data_001.dat file along with other files depending on how many DLCs you have installed. This means that we can extract and inspect all vanilla data directly by using

```powershell
.\BBbuilder.exe extract "path/to/bb/data/data_001.dat" -alt "path/to/new/folder/where/you/want/it/extracted"
```
*Sidenote: the other data files contain a tiny script that informs the code in the main data_001.dat that a DLC is enabled along with sound/music files for those DLC, therefore we don't need to extract those files to look at the code.*

This lets you get a feel for the file structure and is incredibly important for reference when modding. My projects tend to always include the data_001 and MSU folders using sublime text's project feature, so I can jump to function definitions and usages and better understand what I'm trying to do.

Going through each top level folder one by one:

## /scripts
This folder contains squirrel code files (.cnut when compiled, .nut when decompiled) and therefore almost all game logic. This is where the majority of BB modding is done. These are generally loaded alphabetically except for /scripts/config (you can take a look at the order in which files are executed by reading /scripts/preload.nut).

### /scripts/config
These files are loaded first and generally contain static information used by other parts of the game.

## /ui
Contains all the game's JS, CSS and HTML files used to display UI elements to the player. This is where UI modding takes place. 

## /brushes
This folder contains .brush files which you can think of as metadata on how to cut up the big files in /gfx to have them be read by the game. These aren't human readable directly. These files are generally used when showing non-UI visual elements, a good rule of thumb is that if it can move across your screen either due to it moving as part of an animation or due to you moving your screen relative to it, it's using a brush (and is called a 'sprite'), you can think of a brush as a 'skin' for a layer (sprite) of a moving object (usually an entity).

## /gfx
This folder contains all the png files used by the game. There are some larger files used for brushes, and you should read the section for brushes to understand those, as for the others, they can only be accessed from JavaScript.

## /music
Pretty self-explanatory, this folder contains all the longer music files the game uses.

## /sounds
Once again, self-explanatory, containers shorter sound files used by the game.

## /preload
TODO Taro

## /shaders
TODO ???