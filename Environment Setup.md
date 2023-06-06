For beginning your BB modding journey I'd recommend this [video tutorial](https://www.youtube.com/watch?v=Va8Siw5uzNQ) by LordMidas. This will lead you through setting up a code editor (Sublime Text specifically) and explain some vanilla code structure and file decompilation.

This video is starting to show its age a bit and you should do the following things differently:
- Instead of installing the Squirrel package for Sublime Text manually, you should just follow the instructions for automatic installation [here](https://packagecontrol.io/packages/Squirrel), to get the themes shown in the video, you have to manually install the [Battle Brothers Snippets package](https://github.com/Enduriel/Sublime-Text-BB-Snippets) 
- Rather than downloading Adam's tools or creating your own mod folder manually, you should set up [Taro's BBBuilder](https://github.com/TaroEld/BBbuilder), an outstanding command line tool, by following the readme at the link. It also has support for Visual Studio Code. If you do not do this you are massively wasting your time and will struggle to follow the rest of this page.

## BBbuilder
[Github Repository](https://github.com/TaroEld/BBbuilder)
[Latest Release](https://github.com/TaroEld/BBbuilder/releases/latest)

Before beginning modding, I would strongly recommend downloading BBBuilding, creating a mod folder and setting up BBBuilder using 
```powershell
.\BBbuilder.exe config datapath "path/to/battlebrothers/data"
.\BBbuilder.exe config modpath "path/to/folder/for/your/wip/mods"
```
If you've already extracted your game files using bbbuilder's extract function, I'd recommend using
```powershell
.\BBbuilder.exe config folders "path/to/bb/decompiled/files"
```
to automatically have it add that folder to your new projects.

Each time you follow any of the first mod guides, create a new project using
```powershell
.\BBbuilder.exe init mod_my_cool_mod
```
It is convention to prefix your mod folder (and mod ID) with `mod_` and use [snake_case](https://en.wikipedia.org/wiki/Snake_case).

This will create a new folder called `mod_my_cool_mod` in the folder you passed to `config modpath`, create a nice folder structure for you and even create a basic file to get you started with mod_hooks and MSU. If you're using Sublime Text you should be able to open the `mod_my_cool_mod.sublime-project` file and have your environment already configured for you.
