# Recycler

A recycler allows you to display potentially infinite amounts of data in a list (+ grid). When connected with a [Synchronizer](./Synchronizer.md) & [Database](./Database.md), it can cache data in memory (+ storage) and receive updates from the server to whatever data it contains.

## Create a new Recycler
```js
let my_recycler = Recycler({
    list,
    name,
    need = 'default',
    size = 20,
    debug,
});
```

* `name` & `need` is used to save data and make network requests, `name` is usually your `module_name`
* `size` is the number of items fetched in the beginning
* `list` is the List() you created previously

## Parent Views
Be sure to add a parent view to allow your recycler to identify if it is visible or not.
Otherwise it won't load prev / next items on its own. This is to keep visibility checks down to a minimum.

```js
my_recycler.views; // []
my_recycler.add_view( view_name );
my_recycler.remove_view( view_name );
```

## Destroy a Recycler
This happens automatically in Addons upon deactivation.
For Modules, you have to do this manually. Or if you wanna get rid of a recycler with custom logic.
This cancels all pending operations and removes any leftover data.

```js
await my_recycler.destroy();
```

## Add a new entry

Takes an object or an array of objects

```js
my_recycler.set( {
    uid: 'one', // unique ID to refer to later
    title: 'One',
    order: number, // optional
} );
```

* `order` determines the rough position of the object
	* it goes over elements until it finds the nearest order index smaller than or equal to the provided number
	* objects with order index smaller than or greater than `recycler.start` or `recycler.end` are filtered out
	* Recycler can show hints about filtered objects like how many objects were inserted or updated or removed before `recycler.start` or `recycler.end`

## Delete entries

```js
my_recycler.remove_by_uid( 'one' );
my_recycler.remove_all();
```

## Update an entry

```js
my_recycler.set( {
    uid: 'one', // <- old uid to target the previously inserted entry
    title: 'New Title',
} );
```

## Retrieve entries

```js
my_recycler.get_elements();
my_recycler.get_objects();
// IMPORTANT you should check this to filter out the prev/next buttons
my_recycler.is_internal({ uid }); // does this object belong to the recycler
```

## Counts

```js
my_recycler.total_items; // gives you total number of entries
my_recycler.set_count( number );
my_recycler.count();
```

## Using an Adapter

An adapter allows you to override some basic CRUD functionality in your recycler.
`get` to fill-in a missing range of objects
`count` should provide the total number of objects given the filter

```js
let adapter = {
    get: async function ({ start, end }),
    count: async function ({ filter }),
    ...
};
my_recycler.connect_adapter( adapter );
my_recycler.disconnect_adapter();
my_recycler.get_adapter(); // if there's any
```

[Synchronizer](./Synchronizer.md) provides an adapter for Recycler that you can use if you don't wanna implement your own.

## Settings

```js
```

## Events

```js
// *cepts allow filter and overriding requests made by recycler
// called before fetching 'get' or 'count'
// uid is optional, function can be the first arg
recycler.add_intercept( uid, async function ({ need, payload }) {
    return 0; // disallow
    return 1; // allow
} );
recycler.remove_intercept( need );

// called after fetching 'get' or 'count'
// uid is optional, function can be the first arg
recycler.add_postcept( uid, async function ({ need, payload, result }) {
    return 0; // disallow
    return 1; // allow
} );
recycler.remove_postcept( need );

```

## TODO

```js
// create these using set_hook
.before_set
.after_set
// when disabled, show visible hints that it is so, helpful in debugging
```



## Features Planned for the Future

* ...

