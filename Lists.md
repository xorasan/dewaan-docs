# Lists

## Create a new List
```js
let my_list = List( element )
                .id_prefix( my_id )
                .template( template );
```

* `id_prefix` makes the list entries unique, across multiple lists.  `my_id`  will be prepended to the `uid` of all new items
* `template` clone a [template](./Templates.md) for each new entry , by default this is: `"list_item"`, it can be overridden for each entry by setting the `._list_item` property of your object

## Add a new entry

```js
my_list.set( {
    uid: 'one', // unique ID to refer to later
    title: 'One',
} );
```

## Delete entries

```js
my_list.remove_by_uid( 'one' );
my_list.remove_all();
```

## Update an entry

```js
my_list.set( {
    uid: 'one', // <- old uid to target the previously inserted entry
    title: 'New Title',
} );
```

## Retrieve entries

```js
// these return the object
my_list.get_item_object(0); // get by order number
my_list.get_item_object_by_uid('one');
// these return the element
my_list.get_item_element(0); // get by order number
my_list.get_item_element_by_uid('one');
// this will return the DOM keys of an element
my_list.get_item_keys('one'); // returns an object

my_list.length(); // gives you total number of entries
```

## Navigation

```js
my_list.up();
my_list.down();
my_list.left(); // page up
my_list.right(); // page down
my_list.first();
my_list.last();
my_list.scroll_on_focus(1); // scroll when selection changes
```

## Message

```js
my_list.message('No entries'); // hides all entries and shows this message
my_list.message(); // hides the message and shows entries if any
```

## Title

```js
my_list.title('10 entries'); // sets a title
my_list.title(); // removes the title
```

## Selection

```js
my_list.select_by_uid('one');
my_list.select(0); // selects by order number
my_list.select_silently(0); // selects without focussing the list by order number
my_list.select_silently_by_uid('one');
```

## Manipulation

```js
my_list.move_up();
my_list.move_down();
```

## Settings + Layout

```js
my_list.grid(3); // turn the list into a grid, 3 columns, [2, 3, 4, 5]
my_list.grid(); // turn it back into a list
my_list.prevent_focus(1); // prevents the list from becoming focussed on selection
my_list.set_reversed(1); // reverses navigation order
my_list.set_focus(1); // only focussed lists receive key presses
my_list.set_visibility(1);
my_list.select_sk = { ... }; // assign a Softkey object to override the select key
```

## Events

```js
// when an entry is selected
my_list.on_selection = function ( object ) {
    // do something with the object
    // maybe show relevant softkeys?
};
// when a softkey is pressed while your list is focussed
my_list.on_press = function ( object, key, order ) {
    if (key == K.en) { // 'enter' key
        // this is triggered also when you tap on a selected item
    }
};
// you can use this to modify entries being fed into your list
my_list.before_set = function ( object ) {
    // add the current time stamp to the title
    object.title += ' - '+Time.now();
    return object;
};
// after the entry has been inserted, you can make changes to it
my_list.after_set = function ( object, clone, keys ) {
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

## Useful Tips

* Use `.soft_item` on your `template` & it will be given some basic list item behavior like selection, highlight, marking, dynamic padding, Softkey elusion, ...

## Features Planned for the Future

* Select (mark) multiple items, independent of `selection`, `.marked`
* Softkeys that overlap list items should be accurately detected & those specific items should get a temporary padding
  * Make this smooth on scrolling & resize events

* Scrolling algorithm improvement, when an items height is larger than half the screen size,
  it should scroll the container until the next item is at least visible 25% or more before handing off the selection to it
  this gives a better more stable UX

