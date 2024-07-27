# Recycler

## Create a new Recycler
```js
let my_recycler = Recycler( list, name, need = 'default', size = 20 );
```

* `name` & `need` is used to save data and make network requests, `name` is usually your `module_name`
* `size` is the number of items fetched in the beginning
* `list` is the List() you created previouly

## Destroy a Recycler
This happens automatically in Addons upon deactivation.
For Modules, you have to do this manually. Or if you wanna get rid of a recycler with custom logic.
This cancels all pending operations and removes any leftover data.

```js
await recycler.destroy();
```

## Add a new entry

```js
Takes an object or an array of objects
my_recycler.set( {
    uid: 'one', // unique ID to refer to later
    title: 'One',
} );
```

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
recycler.get_elements();
recycler.get_objects();
```

## Counts

```js
recycler.total_items; // gives you total number of entries
recycler.set_count( number );
recycler.count();
```


## Navigation

```js
my_recycler.up();
my_recycler.down();
my_recycler.left(); // page up
my_recycler.right(); // page down
my_recycler.first();
my_recycler.last();
my_recycler.scroll_on_focus(1); // scroll when selection changes
```

## Message

```js
my_recycler.message('No entries'); // hides all entries and shows this message
my_recycler.message(); // hides the message and shows entries if any
```

## Title

```js
my_recycler.title('10 entries'); // sets a title
my_recycler.title(); // removes the title
```

## Selection

```js
my_recycler.select_by_uid('one');
my_recycler.select(0); // selects by order number
my_recycler.select_silently(0); // selects without focussing the list by order number
my_recycler.select_silently_by_uid('one');
```

## Manipulation

```js
my_recycler.move_up();
my_recycler.move_down();
```

## Settings + Layout

```js
my_recycler.grid(3); // turn the list into a grid, 3 columns, [2, 3, 4, 5]
my_recycler.grid(); // turn it back into a list
my_recycler.prevent_focus(1); // prevents the list from becoming focussed on selection
my_recycler.set_reversed(1); // reverses navigation order
my_recycler.set_focus(1); // only focussed lists receive key presses
my_recycler.set_visibility(1);
```

## Events

```js
// when an entry is selected
my_recycler.on_selection = function ( object ) {
    // do something with the object
    // maybe show relevant softkeys?
};
// when a softkey is pressed while your list is focussed
my_recycler.on_press = function ( object, key, order ) {
    if (key == K.en) { // 'enter' key
        // this is triggered also when you tap on a selected item
    }
};
// you can use this to modify entries being fed into your list
my_recycler.before_set = function ( object ) {
    // add the current time stamp to the title
    object.title += ' - '+Time.now();
    return object;
};
// after the entry has been inserted, you can make changes to it
my_recycler.after_set = function ( object, clone, keys ) {
    // keys is DOM keys
    // if you only amend the title here, it'll keep the original object intact
    innertext( keys.title, object.title +' - '+ Time.now() );
    return object;
};
```

## TODO

```js
parent_views
destroy
freeflow
// change .list_item to .set_template
set_scrolling_element
deselect
upon_[start | end | past_start | past_end]
on_focus
is_focussed
adapter
before_removal
hide | show
```



## Features Planned for the Future

* Select multiple items
* ...

