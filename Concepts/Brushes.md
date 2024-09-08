As stated in [[Vanilla Files#/brushes|the brushes section of Vanilla files]], .brush files are effectively metadata for some of the larger pngs in /gfx.

[[Environment Setup#BBBuilder|BBbuilder]] automatically combines this information with the relevant files in /gfx during `extract` to create a new /unpacked_brushes folder which contains folders corresponding to the .brush files. It also automatically recombines this data during `build` so to add new brushes you must follow the same structure as the extracted vanilla files.

Each extracted folder in /unpacked_brushes contains a metadata.xml and a number of png files that are referenced inside of the xml file. The structure of the xml is pretty straightforward:

The first and last lines are mostly just tags to organize the entire structure, and you should copy them directly in your files, with one exception, change the `name=""` tag in the first line to a unique name which will store the big png file that combines all the sprites in your mod. At this time, BBBuilder doesn't work very well with paths that are not stored in the base folder, so prefix the brush name with your mod name instead `name="my_mod_name_my_brush.png"`.

All the other lines in the metadata.xml are references to individual files for separate brushes. For example, let's take a look at the second brush `bust_arrow` in entity_0/metadata.xml. 

This is the arrow pointing at your current character during an ingame turn, it can be referenced in game code using the id `bust_arrow` which is defined by the `id` xml tag. The path to the png to be used for the brush is set using the `img` tag, in this case we can see it references `img="entity\bust_arrow.png"`, and following this path to `/unpacked_brushes/entity_0/entity/bust_arrow.png` we can find the png used by the brush. You may have noticed the other values, other than IC they are all explained by this [excellent image](https://media.discordapp.net/attachments/549994151861682176/938412915973836851/brush_values.png?width=440&height=559) created by Poss. To the best of our current knowledge, the IC value no longer has any impact on how sprites appear so you can safely just copy it from a different sprite.

The usual workflow when creating new sprites for your mod:  
0: Unpack the vanilla brushes/sprites, either using the `extract` command from BBBuilder, or using bbrusher.exe from the modkit.    
1: Search for vanilla sprites that match what you want to add. For example, if you want to add a new socket, which is the bottom plate that units are standing on, you can check out `entity_0/entity/bust_base_player.png` and the corresponding metadata entry in `entity_0/metadata.xml`  
2: Create a new folder structure for your new spritesheet, for example `my_mod/unpacked_brushes/my_mod_tactical_sprites/`, adding the empty metadata file and adapting the `name` property:  
```
<brush name="gfx/my_mod_tactical_sprites.png" version="17" b1="2" b5="0">
</brush>
```
3: Copy the vanilla sprite definition into your metadata file, change the id and the img entries: the id is always just a single string, the img entry is the path to the file relative to the metadata file. You can put the .png next to the metadata file too, in which case it's just the filename:  
```
`my_mod/unpacked_brushes/my_mod_tactical_sprites/my_new_base.png`

<brush name="gfx/my_mod_tactical_sprites.png" version="17" b1="2" b5="0">
  <sprite id="my_mod_my_new_base" offsetY="35" ic="FF464646" width="104" height="142" img="my_new_base.png" left="-49" right="51" top="-71" bottom="-5" />
</brush>
```
4: Edit the .png file  
5: Optionally change the `left right top bottom` parameters in the metadata defintion. These relate to how the sprite is positioned in the game. You can use the [paperdoll website](https://msuteam.github.io/paperdoll/) to help you position the element.  
6: Pack the brush. This is done automatically on `build` when using BBBuilder. Otherwise, you'll have to use the `bbbrusher.exe` application from the modkit, using the `pack` command.  
7: Make sure your newly packed `.brush` file ends up in the `my_mod/brushes` folder, and the spritesheet ends up in the `my_mod/gfx` folder. This is also done automatically by BBBuilder. The folder structure should now look like this:  
```
my_mod/
  brushes/
    my_mod_tactical_sprites.brush
  gfx
    my_mod_tactical_sprites.png
  unpacked_brushes/
    my_mod_tactical_sprites/
      metadata.xml
      my_mod_my_new_base.png
```
8: Use your sprite ingame. Check out how the sprites are set by vanilla by searching the game files. In our example, the base is set in `human.nut` : `this.addSprite("socket").setBrush("bust_base_player");`. So, we'd mirror that somehow for our entity (depends on what your goal is).  
9: Hopefully, it is ingame now.   
