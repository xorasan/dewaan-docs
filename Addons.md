# Addons

To expose an object to other addons, you'll need to make it global.

```js
Addons.add_global(name, object);
Addons.remove_global(name);
```

This will also allow you register a hook that will fire after each time the function is called.
You'll receive:

* `name` of the function
* `module_name` which module called it
* `result` of the function
* `args` the arguments object

```js
Hooks.set('addons-function-call', function ({ name, module_name, result, args }) {
	echo( name, module_name, result, args );
	if (result) {
		
	}
});

```

## Garbage Collection

```js
collect_hook( hook ); // for working with built-in modules
```



## TODO

```js
// doc these
get_addon_context();
'addons-function-call'; // hook use case
```



## Features Planned for the Future

* `publish` your addon
  * get a dev key from Hub, put it in the Addons Dev sheet, once it is approved, you'll see the option to `publish` your addons
  * you can only `publish` addons that you either own or have `set` access to
  * you can `create` a new addon in your Dev node, you'll only be able to `publish` it once it is approved tho
  * each new `push` requires approval
  * a separate dashboard for addons that you've created or have `set` access to

* Hooks should also return their `uid` to avoid collecting the same hooks twice+

