# What this is
This is supposed to be the ultimate repository of Battle Brothers Modding Knowledge. Where good resources are available, those will be linked, when there isn't a good resource, or the resources are lacking I will either write that section from scratch or fill in the blanks as I see fit.
Currently my main goal isn't to make this page easy to follow. The main priority is to make sure that all the possible information you could need is linked to or written here directly

# Engine & Programming Languages
Battle Brothers 'engine' is [Coherent UI](https://coherent-labs.com/Documentation/cpp/db/dbc/_java_script.html), which means it is effectively a browser + an extra Squirrel backend. Our Chromium version is 48 (though we do actually miss *some* features that 48 should have access to) and the browser is used to display UI elements and player interactions with those UI elements. The game logic runs in [Squirrel](http://squirrel-lang.org/) (version 3.0.4) which was created specifically for game dev and isn't very popular outside of it. Help articles for it are scarce and therefore it's recommended to get accustomed to using the [official documentation](http://www.squirrel-lang.org/doc/squirrel3.pdf) as well as electricimps excellent (much better, though occasionally inaccurate due to using a slightly custom version of squirrel) [online reference](https://developer.electricimp.com/squirrel)

### Credits
TaroEld for BBBuilder & MSU
LordMidas for his YT videos & MSU
Poss for his brush metadata.xml image.
