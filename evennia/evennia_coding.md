# Evennia coding notes


## Where to put changes

in the game folder

## For changes to take effect

Reload the server from within the client:

```
reload
```

Or from terminal

```
$ evennia reload
```


To apply typeclass changes to existing objects:

```
@typeclass/force self
```



## Extending objects

### Attributes

Persisted attribs:

`db` is a thing that persists to db

```
self.db.power = 1
self.db.combat_score = 1
```
        
Temporary:

```
self.ndb.tmp_storage = 10
```

### hooks

Put object initialisation not in `__init__` but in
`at_object_creation` method


## Locks

https://www.evennia.com/docs/0.9.5/Locks.html


* make command only available only to the character holding it:

```
self.locks.add("call:holds()")
```


