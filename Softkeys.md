# Softkeys

## Create a new Softkey
```js
Softkeys.add({ n: 'Action Title',
    i: icon_name,
    sh: 'short name' // short
    k: key, // soft left key
    s: 0, // status
	h: 0, // hidden
    c: function () {
    },
});
```

* `id_prefix` makes the list entries unique, across multiple lists.  `my_id`  will be prepended to the `uid` of all new items
* `template` clone a [template](./Templates.md) for each new entry , by default this is: `"list_item"`, it can be overridden for each entry by setting the `._list_item` property of your object



## TODO

```js
Softkeys.add({
    // the default can be up, works for majority of cases
    // when using both, the callback is responsible for accounting for the double/repeat events
    event_type: 'up' or 'down' or 'both',
});
```



## Features Planned for the Future

* ...
* ...

