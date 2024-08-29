# Views

The Views addon expands upon the Views module.

It provides easier abstractions to create stable views with dynamic properties.

```js
let my_view = Views.create({
    name, // required
	// optional
    uid,
    title,
    subtitle,
    icon,
    element
});
```

The only property required is `name`.

```js
// changing title, subtitle, icon will update the Webapp Header & Sidebar
my_view.title = 'My View';
// softkeys can be set & removed
my_view.set_softkey({ ... });
my_view.remove_softkey(uid); // can also take { uid }
// these softkeys will be added whenever the view becomes ready
```



## Garbage Collection

```js

```



## TODO

```js

```



## Features Planned for the Future

* 
  
