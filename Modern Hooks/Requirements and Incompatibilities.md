In Modern Hooks, mod compatibility information is separate from queuing information, though declaring a mod as a requirement or stating that your mod is incompatible is still very simple.



```squirrel
::Hooks.register("mod_my_mod_name", "1.0.0", "Friendly Mod Name!").declareCompatibilityData({
	Requirements = {
		mod_msu = {Version = ">=1.2.0", /*optional name*/ Name = "Modding Standards & Utilities"}
		mod_modern_hooks = {Version = ">=0.1.0"}
	},
	Incompatibilities = {
		mod_swifter = {}
	}
});
```

