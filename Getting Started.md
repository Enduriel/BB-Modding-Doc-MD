Modding Battle Brothers can seem daunting, but honestly it's one of the most straightforward and simultaneously flexible games to mod if you have some knowledge of programming. 

Unfortunately, [Squirrel](http://www.squirrel-lang.org/) specifically is a difficult language to learn directly due to it being quite uncommon. Fortunately, it is very similar to a multitude of C based languages including C, C++, Java and JavaScript, and in my opinion JavaScript is the language you should use to learn programming concepts before getting into Squirrel because resources for it are widely available and it is also used in [[Modern Hooks/UI Modding|UI Modding]].

### Extracting Vanilla Files
Once you have a reasonable grasp of programming. I'd recommend starting by [[Environment Setup|setting up your environment]], especially setting up [BBbuilder](https://github.com/TaroEld/BBbuilder), as without BBbuilder it is quite annoying to manage mods and unpack the vanilla files.

After configuring BBbuilder, you should start by extracting all of the vanilla files into your mods folder. This will both make it easier to reference vanilla code in your own projects (for example by using Sublime Text's [Project format](https://www.sublimetext.com/docs/projects.html) to include the folder), and allow you to browse the vanilla folder structure at will.

We can do this easily by running
```powershell
./BBbuilder.exe extract "path/to/vanilla/data_001.dat"
```
which should create a folder in your previously configured WIP mods directory with the name data_001. For example, in my case I would open powershell to execute
```powershell
./BBbuilder.exe extract "C:/Program Files (x86)/Steam/steamapps/common/Battle Brothers/data/data_001.dat"
```
which would then unzip the data_001.dat file, decompile all the .cnut files into .nut files, and slice up all the big [[Brushes|brush files]] into their individual parts.

Now you can familiarize yourself with the [[Vanilla Files|vanilla file structure]].

## Next steps
I'd recommend going through the Mod Examples to get a feel for how certain mods are made. Also all of the Concepts are pretty important to wrap your head around, so reading through all those pages is also recommended.