# Development

## Glatteis

### TODO

## Addons

### TODO

* Addons UI handles the dev access on Hub & Nodes.
  * On Hub, you can request access, it writes the request in `developers` collection
    * `{ account: uid, status: [-2, -1, 0, 1] }`
    * once approved, it saves a hash to it `{ hash }`
  * On Nodes, you take the approved `hash` from Hub and save it in dev access
    * Dev Access view has a synchro that handles this
    * It saves to `dev_access` collection
    * `{ account: node_account, hash: hub_hash, status: hub_status }`
  * ...
