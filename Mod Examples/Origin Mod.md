// heavy WIP

This is where a lot of people start because it is one of the easiest pieces of content to modify/add. If you've created a new project folder using [[Environment Setup#BBbuilder|BBbuilder]], the first thing you should do is take a look at the file in `scripts/!mods_preload` it automatically created for you. As we are creating a very simple mod which doesn't need to queue or hook any files, we can remove the entire `!mods_preload` folder.

Select a vanilla scenario you'd like to tweak, in this case lets say we want to make the lone wolf scenario start with 10,000 gold. First, we can find the vanilla lone wolf scenario in `scripts/scenarios/world/lone_wolf_scenario.nut`.

***IMPORTANT NOTE***
```
Most Code Editors (including Sublime Text and Visual Studio Code) have a fuzzy file search which makes it much easier to navigate large folders, you can open it in Sublime Text using ctrl+P. You should become accustomed to using it and the general file search (ctrl+shift+F in Sublime Text) to find information about functions, text, files or other data you're looking for.
```

We then copy that scenario to our own mod (while preserving the general path) and rename it to `rich_wolf_scenario.nut`. We should now have the file at the following path in our mod: `scripts/scenarios/world/rich_wolf_scenario.nut`. Now, we can open up the file to make our changes.

The first line of most BB files will start with
```squirrel
this.file_name <-
```
this `file_name` *must* match the name of the file, so we need to change the `lone_wolf_scenario` in our first line to `rich_wolf_scenario`. The first line of your file should now look like:
```squirrel
this.rich_wolf_scenario <- this.inherit("scripts/scenarios/world/starting_scenario", {
```
Each scenario also has a unique ID, so we need to make sure to change the ID of our origin. In the `create` function we should change
```squirrel
this.m.ID = "scenario.lone_wolf";
```
to
```squirrel
this.m.ID = "scenario.rich_wolf";
```
or, to minimize potential clashes with other mods:
```squirrel
this.m.ID = "scenario.mod_rich_wolf.rich_wolf";
```
We should probably also change the name, description and potentially the order of our scenario to match
```squirrel
		this.m.Name = "Rich Lone Wolf";
		this.m.Description = "[p=c][img]gfx/ui/events/event_35.png[/img][/p][p]Wow, look at you! You're a RICH lone wolf, how original.[/p]";
		this.m.Order = 110; // can change this to have it appear higher or lower in the list, completely optional
```
If you're feeling fancy, you can also add a new event image for your event. If you do decide to do this, just place your new image in your mod folder in `gfx/mods/rich_wolf`, for example `gfx/mods/rich_wolf/my_image.png`, then update `this.m.Description` accordingly.
```squirrel
this.m.Description = "[p=c][img]gfx/mods/rich_wolf/my_image.png[/img][/p][p]Wow, look at you! You're a RICH lone wolf, how original.[/p]";
```

Now that all that preparation is done, we can actually perform the change. At the bottom of the `onSpawnAssets` function, you should be able to see a line which starts with
```squirrel
this.World.Assets.m.Money = // some stuff here
```
We can delete the second part of that line and replace it with
```squirrel
this.World.Assets.m.Money = 10000
```
and we're done.
Now to actually test our mod, we can use BB // TODO

If you look around this part of the code, you should be able to see some other values you might like to change, like the starting items, the default stash size, the bro's level and the like. Feel free