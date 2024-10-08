# Synchronizer

Synchronizer lets you sync objects between the client & the server on the same node.
It is built on top of the Network API (sync+intercept+get) & Sockets API (emit). With it, you can focus on your Addon's logic, instead of the sync logic.

## Primary Use Case

You have an array of ordered data on your server. You want to modify and keep it synced with all clients connected to your server.

## API

It offers almost the same API on both sides. A `Synchronizer` constructor.

## Client

### Create

```js
let my_synchro = Synchronizer({
    name,
    recycler, // optional
    recycler_view, // optional, default view for recycler (uses name by default)
    list: { // optional, will create auto-create a List & Recycler for you
        element: <HTMLElement>, // required
        id_prefix: 'string', // optional, default: name
        template: 'string', // optional, default: 'list_item'
    }
});
```

* `name` is used to save data and make network requests, `name` is usually your `module_name`, also used to identify requests between client & server, whether they're coming from the same synchronizer or not.
* `recycler` is the [Recycler](./Recycler.md)() you created previously, you can either provide a `recycler` or `list`
* `list` will create a `List()` & wrap it in a `Recycler()` & make both available as `my_synchro.list` & `my_synchro.recycler`
  * it overrides `uponpaststart` & `uponpastend` to prevent navigation loops


Synchronizer will create databases with the `name+need` combos. It'll connect its adapter to the provided `recycler`.

### Events

On the client, before items are saved to Database, you can intercept:

* `sanitizer`

### Examples

* `sanitizer` incoming objects are validated to expected structure and types

	```js
	let my_sanitizer = my_synchro.add_sanitizer(async function ( { object } ) {
	    delete object.prop;
	});
	// you can remove it later if need be
	my_sanitizer.remove();
	```


## Server

### Create

```js
let my_synchro = Synchronizer({
    name,
    module_name, // optional, auto-assigned using addons-function-call
    disallow_set, // optional, false by default
    disallow_creation, // optional, false by default
    disallow_update, // optional, false by default
    disallow_removal, // optional, false by default
});
```

* `name` make sure it matches the one of the client side
* `disallow_creation` & `disallow_removal`
  * use these if you want to limit to specific pre-`set` objects on Server
  * both can be overridden in `before_save` & `before_removal`
* `disallow_update` only disallows updates, creation is allowed unless explicitly spec'd
* `disallow_set` also disallows creation & update


### Variants

On Server, variants are activated per socket. The default variant is called `default`, so be careful with overriding it.

```js
my_synchro.add_variant( filter );
// This is to be called from a socket context event
my_synchro.activate_variant( name ); // cancels all pending operations
my_synchro.remove_variant( name ); // activates the default variant
my_synchro.active_variant; // readonly property
```



### Events

On the server, Synchronizer sets up hooks to receive network requests like `sync` and `get`.
It provides events to intercept its logic:

* `counter`
* `validator` incoming objects are validated to expected structure & types
  * you **should** return `{ valid, object }`
  * if `valid` is `0`, the object is marked invalid, the object will be saved either way to allow collaboration to fix mistakes `.invalid = 1`
  * if `valid` is `false`, the object is rejected & skipped, it won't be saved or removed, ask user to try again, `.rejected = 1`
  * you **should** also use this step to discard extra (potentially malicious) data & ensure correct structure
  
* `before-get` & `after-get` when Client requests objects
  * `before-get-range` & `after-get-range`
  * `before-get-by-uids` & `after-get-by-uids`

* `before-save` & `after-save`
  * `before_save` is used for access control, return 1 to allow, 0 to reject
  * `after_save` can be used to save a copy of the object somewhere else
* `before-removal` & `after-removal`
* `sanitizer ({ connection, objects[, active_variant, start, end, args] })`
  * this is triggered whenever objects are about to be sent out to a Client
  * you can use it to delete or change properties
  * `active_variant, start, end, args` are not always available
  
* `reporter`


## For both Server & Client sides

### Destroy a Synchronizer

This happens automatically in Addons upon deactivation.
For Modules, you have to do this manually. Or if you wanna get rid of a synchronizer with custom logic.
This cancels all pending operations and removes any leftover data.

```js
await my_synchro.destroy();
```

### Reserved Properties

Reserved properties for objects that you should only override if you know what you're doing:

* `uid`
* `rejected`
* `unsaved` failed to save, set by Server
* `unremoved` failed to remove, set by Server
* `invalid`
* `pending`
* `remove` can be
	* `1` an object is to be removed, Client sends this to Server, can also be used to signal trash
	* `-1` an object is to be purged, Server sends this to Client to signal full removal
* `removed` ms when the object was removed
* `order` an integer representing the sort order

## Advanced

```js
my_synchro.module_name; // the Addon that calls this is automatically assigned here
```

* `module_name` helps when your module gets deactivated, it automatically destroys any Synchronizers you created.

# TODO

## Near

* save ranges per variant, deliver updates per variant

* Client should be able to subscribe to variant updates it's interested in

  * ```js
    my_synchro.enable_variant( varant_name );
    my_synchro.disable_variant( varant_name );
    ```

* periodic removal & its hooks

* **DONE** creating new objects

  * `ruid -> uid` conversion needs to be handled

* `get-range`

  * **DONE** `before-get-range` & `after-get-range` need to be implemented, same with `get-count`
  * support for `has_more` & `infinite` list strategy


## Far

* add support for syncing binary buffers like images, audio videos...
* divide buffers into smaller chunks to sync
* allow support for mid-way resumption of broken uploads and downloads
* progress report per object



















