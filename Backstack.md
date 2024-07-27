# Backstack

## Create
```js
Backstack.add({ n: 'Action Title',
    i: icon_name,
    k: key, // soft left key
    c: function () {
    },
});
```

* `id_prefix` makes the list entries unique, across multiple lists.  `my_id`  will be prepended to the `uid` of all new items
* `template` clone a [template](./Templates.md) for each new entry , by default this is: `"list_item"`, it can be overridden for each entry by setting the `._list_item` property of your object

## TODO

```js
```



## Features Planned for the Future

* ...
* ...

