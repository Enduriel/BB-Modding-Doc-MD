// heavy WIP

This is where a lot of people start because it is one of the easiest pieces of content to modify/add. If you've created a new project folder using BBBuilder, the first thing you will need to do is delete the `scripts/!mods_preload` folder it automatically created for you, as in this case (and probably only in this case) we will not need it.

Select a vanilla scenario you'd like to tweak, in this case lets say we want to make the lone wolf scenario start with 10,000 gold. First, we can find the vanilla lone wolf scenario in `scripts/scenarios/world/lone_wolf_scenario.nut`.

***IMPORTANT NOTE***
```
Most Code Editors (including Sublime Text and Visual Studio Code) have a fuzzy file search which makes it much easier to navigate large folders, you can open it in Sublime Text using ctrl+P. You should become accustomed to using it and the general file search (ctrl+shift+F in Sublime Text) to find information about functions, text, files or other data you're looking for.
```

we then copy that scenario to our own mod (while preserving the general path) and rename it to `rich_wolf_scenario.nut`. We should now have the file at the following path in our mod: `scripts/scenarios/world/rich_wolf_scenario.nut`. Now, we can open up the file to make our changes.

The first line of most BB files will include 