Battle Brothers uses a unique implementation of Object Oriented Programing, separate from the standard [squirrel class](https://developer.electricimp.com/squirrel/squirrel-guide/classes). Effectively, each 'class' is a [table](https://developer.electricimp.com/squirrel/table) (Similar to [JavaScript Objects](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)), and each 'instance' is also a table which has been [cloned](https://developer.electricimp.com/squirrel/keywords#clone) from the original 'class'. Tables that inherit from parents have their parents set using Squirrel's [setdelegate](https://developer.electricimp.com/squirrel/table/setdelegate) function upon instantiation.
## Instantiation
When a BB Class is instantiated, it
1) [Clones](https://developer.electricimp.com/squirrel/keywords#clone)  the prototype object it is an instance of
2) Clones the m table of the prototype object it is an instance of
3) Repeats steps 1-2 for all its [[#Inheritance|ancestors]]
4) Executes the `create` function

in some special cases, such as if the BB Class is instantiated by the backend (this mostly happens to world and tile entities) the following then also happens.
5) Some special native functions are added to the table.
6) Executes the `onInit` function.

## Inheritance
When a table is still a 'class' we refer to it as a prototype object, in this state it doesn't have it's delegation set up, and therefore calling [getdelegate](https://developer.electricimp.com/squirrel/table/getdelegate) on it will return null. However, it does contain a clone of its parent as a value in the prototype table with the key being the shorthand parent's name.

For example, `scripts/items/weapons/weapon` inherits from `scripts/items/item`, so you can access a clone of the `scripts/items/item` BB Class inside a weapon using `<bbclass>.item`. To instead dynamically resolve the parent of the current item we can use `<bbclass>.SuperName` which would in this case be `"item"` and therefore access our parent using `<bbclass>[<bbclass>.SuperName]`. This can return unexpected results when used inside of the function body of a function in a BB Class, as this function can be called by a descendant of the class it was already placed in and in that case `<class>[<class>.SuperName]` could actually be referencing the very table the function is in, which could lead to infinite recursion.

## Performance

Despite being incredibly inefficient (~40x more memory usage and ~50x slower to instantiate than native squirrel classes according to my testing), BB objects have one advantage over more traditional squirrel classes: it is possible to modify the functions of an already instantiated BB object. This can be used for a so called [[Switcheroo Hook]]