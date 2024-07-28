# Synchronizer

## Client

### Create

```js
let my_synchronizer = Synchronizer({
    name,
    recycler,
});
```

* `name` is used to save data and make network requests, `name` is usually your `module_name`, also used to identify requests between client & server
* `recycler` is the Recycler() you created previously

Synchronizer will create databases with the `name+need` combos. It'll connect its adapter to the provided `recycler`.

## Server

### Create

```js
let my_synchronizer = Synchronizer({
    name,
});
```

* `name` make sure it matches the one of the client side

On the server, Synchronizer sets up hooks to receive network requests like `sync` and `get`.
It provides events to intercept its logic:

* `validate` incoming objects are validated to expected structure and types

	```js
	let my_validator = my_synchronizer.add_validator(async function ( object ) {
	    // maybe you wanna check if it already exists somewhere
	    await does_object_already_exist( object );
	    // if object is valid
	    return 1;
	    // if it's invalid, this will reject the object and return it to the client with a failed property
	    // changes you make to the object will still be sent out to the client
	    return 0;
	    // you can also return a modified version to signal it's valid
	    object.prop = 'something';
	    return object;
	});
	// you can remove it later if need be
	my_validator.remove();
	```

* `save` use this to save the objects somewhere

	```js
	let my_saver = my_synchronizer.add_saver(async function ( object ) {
	    // save it somewhere
	    await save_object( object );
	    // if it got saved successfully
	    return 1;
	    // if it failed to save, you can also attach an error message to the object
	    object.error = 'why it failed';
	    return 0;
	});
	// you can remove it later if need be
	my_saver.remove();
	```

* `respond` is called before the objects are sent back out to the client

	```js
	let my_responder = my_synchronizer.add_responder(async function ( object ) {
	    // you can use this to remove server related properties from the object
	    delete object.some_secret_prop;
	});
	// you can remove it later if need be
	my_responder.remove();
	```

	





## For both Server & Client sides

### Destroy a Synchronizer

This happens automatically in Addons upon deactivation.
For Modules, you have to do this manually. Or if you wanna get rid of a synchronizer with custom logic.
This cancels all pending operations and removes any leftover data.

```js
await my_synchronizer.destroy();
```

## 









