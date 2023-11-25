Logging is an incredibly part of mod development in an environment where we do not have access to the program state in real time using an IDE or web browser console. I would strongly recommend that you download and install [TaroEld's Dev Console](https://www.nexusmods.com/battlebrothers/mods/380) mod to allow you to run code while the game is running.

Battle Brothers creates a log.html file (which should be opened using a browser), and places it in
- `C:\Users\<YourName>\Documents\Battle Brothers\log.html` by default
- `~/.steam/debian-installation/steamapps/compatdata/365360/pfx/dosdevices/c:/users/steamuser/Documents/Battle Brothers/log.html` if you're using Proton on Linux

All of the below write to log.
## Squirrel
There are multiple functions to print to console in squirrel, they are only differentiated by the color they output and the short descriptor to the left of the text in the log. They each accept a [Squirrel String](https://developer.electricimp.com/squirrel/string) as their parameter Their individual use is self-explanatory from their name:

- `::logInfo()` blue
- `::logDebug()` yellow
- `::logWarning()` orange
- `::logError()` red
### Example
```squirrel
::logInfo("Hi this is some information!");
if (someCheck)
	::logDebug("something happened that I'd like to be informed about in log");
::logWarning("Hi this is a warning");
::logError("Something went very wrong!");
```

## JavaScript
While the standard [console](https://developer.mozilla.org/en-US/docs/Web/API/console) object exists and seems to behave as normal in JS. Only calls to `console.error` actually print to log.

We also have access to the [JSON](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON) object and can use that to convert a [JavaScript Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) into a human-readable string. 

Additionally, we can use queryStackTrace() to get a string representation of the JavaScript Stack trace. This just uses [Error().stack](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/stack#browser_compatibility) under the hood.
### Example
```js
console.error("This is me printing something to the log from JavaScript");
console.error("This is me printing a JS object using JSON.stringify: " + JSON.stringify({
	key : "value",
	int : 1,
	bool : false,
	nothing : null
}));
console.error("I'd like to print the stack trace:\n\n" + queryStackTrace());
```

## Utilities
I would strongly recommend using [[MSU|MSU's]] [Logging](https://github.com/MSUTeam/MSU/wiki/Logging) utilities when trying to debug the game as they allow you to nicely print [all Squirrel Types](https://github.com/MSUTeam/MSU/wiki/Logging#printdata) and [the stack trace](https://github.com/MSUTeam/MSU/wiki/Logging#printstacktrace) from Squirrel code.