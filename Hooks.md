# Hooks

## Until

For cases where you need feed arguments directly, use `Hooks.until`

```js
async function before ({ name, need, hook, hook_id }) {
    return 1; // yes
}
let args = [1, 2, 3];
await Hooks.until('hook-name', ...args);
```

## Until Many

For cases where you need feed different arguments to many handlers individually, use `Hooks.until_many`

```js
async function before ({ name, need, hook, hook_id }) {
    return 1; // yes
    return [1, 2, 3]; // an array of arguments
}
let results = await Hooks.until_many('hook-name', { before });
// results
{
    hook_id: { name, need, result },
    ...
}
```




## TODO

```js
Hooks.until_many('hook-name', { before, name, need }); // name and need to target specific handlers
```



## Features Planned for the Future

* ...
* ...

