This advanced type of hook uses the fact that all instances of [[Battle Brothers Class|BB classes]] are also tables, rather than squirrel instances. This means we can modify the fields and functions of the bb objects during runtime. This has much greater performance implications than any [[Basic Hooks|normal type of hook]], but sometimes it is the only alternative to completely overwriting a function and therefore negatively impacting mod compatibility.

This concept is easiest to explain with an example:
```squirrel
::Hooks.
```