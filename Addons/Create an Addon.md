# Create an Addon

This tutorial assumes that you have at least an extremely basic understanding of the following:
* Installing & running Dewaan
* Sidebar
* Views
* Sheets
* Dialogs
* Softkeys
* Webapp
* Network
* Hooks & the order in which Webapp fires them at startup
* Backstack & how it utilizes the History API

The tutorial aims to use these components in a way that'll make them easy to situate in your memory,  
& give you the power to make the most out of them!

For starters, we'll create a Tasks addon, it will be able to do the following:

* Add, remove and edit tasks
* Get the most recent tasks from the server

These are the major steps we'll take to create the addon:

* Create the Skeleton
* Setup the Client side (setup the Views, Sheets, Lists, Softkeys, Network, ...)
* Setup the Server side (respond to Network requests, save to Database, ...)
* Publish your Addon

## Create the Skeleton

* Create a **tasks** folder in **released/addons**
* Create **manifest.w**
* Copy **mudeer/icons/doneall.svg** here and rename it to **icon.svg**
* Create **client.js** and a folder called **client**
	* Create **client.htm.w** & **client.css.w** inside the **client** folder

## Setup the Client

### The base

Do read the comments in the code, they're vital in understanding the fundamentals.  
Start by setting up your **client.js** to look like this:

```js
;(function(){ // wrap your code with this IIFE to prevent pollution of the global JS scope
	'use strict';

	// we'll export our Addon's API to other Addons & Modules later
	let Tasks = {},
		// we'll use this to filter in relevant hooks
		module_name = 'tasks';

	// we'll put the hooks here
})();
```

### Hook into Activation & Disable events

Next we'll add two hooks, **addon-activate** & **addon-disable**
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

### Add your 






















