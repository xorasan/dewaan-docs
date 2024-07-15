# Lists

## Create a new List
```js
let my_list = List( element ).id_prefix( my_id ).list_item( template );
```

`id_prefix` makes the list entries unique, across multiple lists.  `my_id`  will be prepended to the `uid` of all new items

## Add a new entry

```js
my_list.set( {
    uid: 'one',
    title: 'One',
} );
```



On selection changes:

