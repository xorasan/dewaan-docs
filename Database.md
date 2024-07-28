# Database
Save objects in memory or storage with auto syncing.

## Client
```js
let my_store = Database.store(name, {
	sync: true, // sync pending items to server, false by default
	auto_sync: true, // whenever items are added or removed client side, false by default
});
```

Whenever you save an object marked pending, it'll be passed on to Network.sync in an array.  
On the server side you have to return every single item you received to allow database to  
confirm that the sync was successful, this way it can reliably mark items as synced.

You receive an array on the server side and you return a modified version of it.

```js
Hooks.set('network-sync', module_name, async function (response) {
	let out = [];
	if (isarr( response.value )) {
		response.value.forEach(function (o) {
			o.pending = 0;
			out.push(o);
		});
	}
	return out;
});
```


You can listen for sync changes using:

```js
Hooks.set('database-sync', [name, need], async function ({ action, object }) {
});
```

Add an object to store:
```js
my_store.set( object );
```
