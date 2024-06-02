# Create an Addon

## Requirements

This tutorial assumes that you have at least an extremely basic understanding of the following:
* Installing & running Dewaan (the development version)
* Setup Codelite & add Mudeer as external tools
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












