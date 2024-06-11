# Create an Addon

## Requirements

This tutorial assumes that you have at least an extremely basic understanding of the following:
* Installing & running Dewaan (the development version)
* [Setup Codelite & add Mudeer as external tools](Setup Codelite.md)
* Sidebar
* Views
* Sheets
* Dialogs
* Softkeys
* Webapp
* Network
* Hooks & the order in which Webapp fires them at startup
* Backstack & how it utilizes the History API

The tutorial aims to use these components in a way that'll make them **easy to remember**,  
& give you the power to make the most out of them!

Each step builds upon the previous one so **take your time** & glean as much as you can from each step. :)

## Mission: `Tasks` Addon

For starters, we'll create a Tasks addon, it will be able to do the following:

* Add, remove and edit tasks
* Get the most recent tasks from the server

## Plan
These are the major steps we'll take to create the addon:

* Create the Skeleton
* Setup the Client side (setup the Views, Sheets, Lists, Softkeys, Network, ...)
* Setup the Server side (respond to Network requests, save to Database, ...)
* Publish your Addon

## Create the Skeleton

* Create a `tasks` folder in `released/addons`
* Create `manifest.w`
* Copy `mudeer/icons/doneall.svg` here and rename it to `icon.svg`
* Create `client.js` and a folder called `client`
	* Create `client.htm.w` & `client.css.w` inside the `client` folder

Your directory tree should end up looking like this:
```
released/
	addons/
		tasks/
			manifest.w
			icon.svg
			client.js
			client/
 				client.htm.w
 				client.css.w
```

## Manifest your Addon

Add this to you `manifest.w`

```
name			Tasks
description		Get things done with your team!
build			1
```

In Dewaan, go to `Addons` & you'll see `Tasks` show up there!  
*You might have to reload Dewaan in the browser if it doesn't show up on its own*

## Setup the Client

### The base

Do read the comments in the code, they're vital in understanding the fundamentals.  
Start by setting up your `client.js` to look like this:

```js
;(function(){ // wrap your code with this IIFE to prevent pollution of the global JS scope
	'use strict';

	// REMEMBER this "addon globals" section, we'll add more here later
	// we'll export our Addon's API to other Addons & Modules later
	let Tasks = {},
		// we'll use this to filter in relevant hooks
		module_name = 'tasks',
		// to export as a Dewaan-wide global, also title for the Sidebar entry, ...
		module_title = 'Tasks';

	// we'll put the hooks here
})();
```

### Hook into Activation & Disable events

Next we'll add two hooks, `addon-activate` & `addon-disable`
Any resources we acquire must be released or destroyed when our addon is disabled. This also helps prevent memory leaks.


```js
let hook_activate = Hooks.set('addon-activate', async function ( { uid } ) { if (uid == module_name) {
	// this hook is triggered whenever your addon is activated, including on Webapp startup
	
} });
let hook_disable = Hooks.set('addon-disable', async function ( { uid } ) { if (uid == module_name) {
	// remove the hooks we set upon startup to avoid trigger errors while our addon is not loaded
	hook_activate.remove();
	hook_disable.remove();
	
} });

```

These hooks will notify you when ANY addon is activated or disabled, that's why we check if `uid == module_name`.

### Add your Addon to the Sidebar

Add `Sidebar` to the `addon globals`.  
```js
let Tasks = {},
	Sidebar,
	...
```

In the `addon-activate` hook, check if `Sidebar` is available, assign it to the addon global `Sidebar`.  
If it is available, we'll add our `Tasks` entry 

```js
Sidebar = get_global_object().Sidebar;
if (Sidebar) {
	Sidebar.set({
		uid, // or module_name (= 'tasks')
		title: module_title, // Tasks
		icon$h: Addons.get_icon(module_name), // this will get us icon.svg
		// the $h suffix treats the provided value as raw HTML
	});
}
```

In Dewaan, (after a reload), if you tap on the `Tasks` entry in the Sidebar, it won't do much,  
in the browser console (F12 DevTools), you'll see "View not found", so let's...

Also remove your addon's entry from the Sidebar when your addon is being disabled.  
Add this to the disable hook:
```
Sidebar.remove(uid);
```

### Create the `tasks` View

In `client.htm.w`

```bash
[view=tasks]
	b "To make it obvious! This is the Tasks View, remove this bold element later"
```

Check Dewaan again (reload), you'll see this view show up whenever you tap on `Tasks` in the Sidebar.

Although it still doesn't show our icon or title in the header, so let's handle that...

### Webapp Header Title & Icon

Add the `view-ready` hook & whenever our tasks view becomes ready & fully active (nothing blocking it),  
we set the Webapp's header to our module's title along with our `icon.svg`:

```js
let hook_view_ready = Hooks.set('view-ready', async function ( { uid } ) { if (View.is_active_fully( module_name )) {
	// this sets the header title, subtitle and the icon
	Webapp.header([ module_title, 0, Addons.get_icon(module_name) ]);
} });
```
Also be sure to `remove` this new hook in the `addon-disable` hook:

```js
	...
	hook_view_ready.remove();
	...
```

### Setup the Tasks List

Add `tasks_view_keys` & `tasks_list` to the `addon globals`, & get the keys
```js
let Tasks = {},
	Sidebar,
	tasks_view_keys,
	tasks_list,
	...
```

Get the View dom keys in the `addon-activate` hook
```js
tasks_view_keys = Views.dom_keys( module_name );
```

Create a Template called `task_item` in `client.htm.w`
```js
[template=task_item] .soft_item .task_item .flex
	[id=uid] .dim .pad
	[id=created] .pad
	[id=tag] .tag .pad
	[id=note] .pad
```
*Notes on the css classes:*
* `soft_item`, `pad`, `dim`, `tag`, `flex` are all Mudeer standard classes
* `soft_item` gives List items the default Mudeer look ;)
* `tag` makes an element look like a #hashtag
* `task_item` is something we created just now, we'll use it in `client.css.w`


Create a List there & add it to the addon globals in the `addon-activate` hook
```js
tasks_list = List( tasks_view_keys.list )
				// each list item has an dom #id, this prefix helps keep it unique per list
				.id_prefix( module_name )
				// assign our task_item Template to this list
				.list_item( 'task_item' )
			;
```

Create a Softkey to add dummy tasks in the `view-ready` hook
```js
Softkeys.add({ n: 'New Task', // name
	i: 'iconadd', // icon
	k: K.sl, // key: soft left (on smart feature phones), F1 on Desktops
	c: function () { // callback
		// we'll replace this with a Sheet call
		tasks_list.set({ // set/add/create an item
			uid: 'one'+Time.now(), // Time.now() returns ms since epoch to keep this item unique
			tag: 'DONE',
			note: 'Do something...',
			created$time: Time.now(), // $time is a Mudeer/Templates helper, it makes the time human friendly and dynamically updates it
		});
	},
});
```

The reason we added this in the `view-ready` hook?  
* Softkeys are contextual
* Each View, Sheet & Dialog can have its own softkeys, they also have defaults

Create a Sheet to define new tasks better
```js
[sheet=new_task] .pad
	input [id=tag] @placeholder(Tag)
	input [id=note] @placeholder(Note)
```

Replace the `New Task` softkey's callback with this:
```js
Hooks.run('sheet', 'new_task');
```


Add these hooks to setup the sheet and once the sheet is "done" you can add a task based on the values entered:
```js
let hook_sheet_ready = Hooks.set(sheet_ready, async function (args, k) { if (args.name == new_task_sheet) {
	Sheet.set_title('Setup Task');
	k.tag.value = 'TODO';
	k.note.focus();
} });
let hook_sheet_done = Hooks.set(sheet_done, function (args, k) { if (args.name == new_task_sheet) {
	let o = {
		uid:			'task'+Time.now(),
		created$time:	Time.now(),
		tag:			k.tag.value,
		note:			k.note.value,
	};

	tasks_list.set(o);
} });
```

Remember to remove the hooks in `addon-disable`
```js
hook_sheet_ready.remove();
hook_sheet_done.remove();
```

Create another Softkey to delete tasks
```js
Softkeys.add({ n: 'Delete Task',
	i: 'icondeleteforever',
	k: 'delete',
	c: function () {
		tasks_list.pop(); // this removes whatever item is selected
	},
});
```

Create yet another Softkey to mark a task as "done"
```js
Softkeys.add({ n: 'Mark Done',
	i: 'icondone',
	k: 'd',
	ctrl: 1,
	c: function () {
		let task = tasks_list.get_item_object(); // get the selected item's linked object
		task.tag = 'DONE';
		tasks_list.set(task);
	},
});
```


* TODO Add a dialog to ask before deletion












