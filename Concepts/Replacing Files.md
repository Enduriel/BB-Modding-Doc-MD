***IMPORTANT NOTE***
```
This style of modding is highly discouraged and the use of hooks is recommended instead. This page is here for purely educational purposes to explain how this systems works and to help with maintaining mods relying on ancient modding practices.
```

The most basic form of Battle Brothers modding is file replacements. The basic idea being somewhat similar to editing the vanilla data_001.dat directly, except we instead create our own top level mod zip file, and then inside of that copy the exact path of the vanilla file we're trying to change. The game will then usually (based on the rules described further below) read the file we provide instead of the original files.

This approach is popular for new modders due to its extreme simplicity and it often being the only option when modding other games. However it makes it very difficult to keep mods compatible with each other as well as being likely to break with game updates.

As explained [[Home#Engine & Programming Languages|here]], the engine used by BB is Coherent UI. When this engine loads the Battle Brothers mods and data files, it follows a specific set of rules to prioritize which files overwrite which other files in what situations. There are 3 factors that determine overwriting priority:
- Mod zip file name
- Last edited file metadata for the file
- Whether the file is compiled or decompiled (.cnut or .nut)

### Overwrite Priority
If filetypes are the same (both .cnut or both .nut) A mod zip file with a name that would be sorted after another mod zip file name by alphabetical sort order will overwrite that file:
- A file in z_mod_mymod.zip will overwrite a file in mod_mymod.zip
- By the same logic, since all mod files should begin with mod_, they will overwrite the vanilla data_ files.

If filetypes are different (one cnut/one nut), then whichever has a more recent 'Date Modified' will be prioritized.