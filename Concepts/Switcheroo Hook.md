This advanced type of hook uses the fact that it is possible to modify Squirrel tables on the fly, and since [[Battle Brothers Class|BB Classes]] are also tables they can be modified too. This means we can modify the fields and functions of bb objects during runtime. This has much greater performance implications than any [[Basic Hooks|normal type of hook]], but sometimes it is the only alternative to completely overwriting a function and therefore negatively impacting mod compatibility.

This concept is easiest to explain with an example, say we'd like to modify the minimum chance to hit, by making it 10% instead of 5%. Without switcheroo hooks this would require us to use a [[Basic Hooks#Wrapping Functions|wrap function hook]] to replace the original function (`attackEntity` in `scripts/skills/skill`) and fully overwrite it, this is obviously undesirable as only one mod can do this and it can easily break with game updates. Instead, we could take a look at the function and notice that it uses
```squirrel
this.Math.max(5, this.Math.min(95, toHit))
```
to calculate the ceiling and floor for the chance to hit. So, if we were to replace the `::Math.max` function, and make it so that if the first parameter is a 5 we treat is as a 10, we would be able to change the minimum hit chance. We could do this relatively easily using a [[Basic Hooks#Wrapping Functions|wrap function]] hook
```squirrel
::Hooks.wrapFunctions("mod_my_mod", "scripts/skills/skill", {
	function attackEntity( _originalFunction )
	{
		return function( _user, _targetEntity, _allowDiversion = true ) {
			local max = ::Math.max; // store the original function locally
			::Math.max = function (_a, _b) { // replace with our wrapper
				if (_a == 5) // if the first parameter is 5, we increase it to 10
					_a = 10;
				return max(_a, _b); // return the original result
			}
			local ret = _originalFunction( _user, _targetEntity, _allowDiversion) // run the function we are hooking
			::Math.max = max; // restore the original function
			return ret; // technically unnecessary in this case but is easy to forget in other situations
		}
	}
});
```