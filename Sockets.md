# Sockets

**TODO** What does it do?

## Hooks

Hooks used by this addon.

```js
// run
sockets
// await until
// await until_many
// set
// async set
```

### Until

```js
await Sockets.until([name, need], ...args); // Client
Hooks.set('sockets-until', [name, need], (connection, ...args) => {}); // Server
```



# TODO

```js
Sockets.emit();
Sockets.emit_by_context();
```





















