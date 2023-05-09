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

## Exploring / troubleshooting

### REPL

Run the shell (requires ipython)
`evennia shell`

Then:

```
import evennia
evennia.TAB
```

### Logging

logs are all directed either to stdout (if Evennia is running in interactive mode) or to $GAME_DIR/server/logs.

```
evennia.utils.logger.log_err(errmsg)
evennia.utils.logger.log_warn(warnmsg)
evennia.utils.logger.log_info(infomsg)
```


## The API

The API is "flattened" into a single module, the toplevel `evennia` module.
Everything you need is here and can be inspected [https://www.evennia.com/docs/0.9.5/api/evennia.html#module-evennia|here]
Alternatively, use the shell/REPL to explore it.




## Ojbects

All objects extend from `evennia.objects.objects.DefaultObject`. This class defines the object api.





This is the root typeclass object, representing all entities that have an actual presence in-game. DefaultObjects generally have a location. They can also be manipulated and looked at. Game entities you define should inherit from DefaultObject at some distance.


Properties
* contents: holds List of all objects that has this object set as its location
* exits: 

Methods:
* search(searchdata, global_search, ...) 
  * Will be matched against object.key (with object.aliases second) unless the keyword attribute_name specifies otherwise.
  * Special strings:
    * `#<num>`: search by unique dbref. This is always a global search.
    * `me,self`: self-reference to this object
    * `<num>-<string>` - can be used to differentiate  between multiple same-named matches. The exact form of this input is given by settings.SEARCH_MULTIMATCH_REGEX.
  * global_search (bool) - search everywhere, otherwise with the object WARNING this didn't work as expected when I tested it
  
     
* msg(text, ...): Emit something
* msg_contents(text=None, ...):
   * emit message to all objects inside this object
* move_to(...)
* clear_contents() - move all objects inside to their home positions
* copy()
* delete()
* access()
* at_msg_receive(text=None, from_obj=None, **kwargs) - whenever someone sends a message to this object using the msg method.
* return_appearance(looker, **kwargs) - This formats a description. It is the hook a ‘look’ command should call.


### Checking the type of an object


```
is_container = item.is_typeclass("typeclasses.hogwarts_objects.WandSpawningChest", False) # true
# checking the parent class also worked
is_container = item.is_typeclass("typeclasses.hogwarts_objects.GenericContainer", False)

```

These two give the same result:

```
type(item)
item.typeclass_path
```
  



## Announcing events

See object api msg and msg_contents

Additional for msg_contents:

Say Char is a Character object and Npc is an NPC object:

char.location.msg_contents(

    “{attacker} kicks {defender}”, mapping=dict(attacker=char, defender=npc), exclude=(char, npc))

This will result in everyone in the room seeing ‘Char kicks NPC’ where everyone may potentially see different results for Char and Npc depending on the results of char.get_display_name(looker) and npc.get_display_name(looker) for each particular onlooker

### Examples

#### Commands being performed on objects

```
self.obj.location.msg_contents(f"{self.caller} opens the {self.args}.", exclude=self.caller)
self.caller.msg(f"You open the {self.args}.")
```
   
#### Objects changing state

```
self.location.msg_contents("The chest opens.")
```
             

## Extending objects

Must extend from `DefaultObject`

### Initialisation options

* `at_object_creation` - called once when object is created
	* 
* `at_init` - called when object is loaded into memory
	* typicallly use the `self.ndb.*` attributes here
	* use when you would normally use `__init__`
	

### Magic properties

Some of the properties in the object is automatically save to db:

`key` - in django actually called `db_key`
`date_created`
`typeclass_path`
`id` - numeric id of db row assigned by Django
`dbref` - returns value of `id` but in str format with `#` in front

`tags` - `tags.add()`, `tags.get()`
`locks` - `locks.add()`, `locks.get()`
`attributes`  - `attributes.add()`
`db` - shortcut for `attributes`, can use `obj.db..attrname = value`
`ndb` - `obj.ndb.attrname = value`


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


### Search for objects

https://www.evennia.com/docs/0.9.5/Tutorial-Searching-For-Objects.html

In the most common case, use the search method of the Object or Account typeclasses. 
In a command, the `.caller` property will refer back to the object using the command (usually a Character, which is a type of Object) 
`.args` will contain Command’s arguments. SEE `search` method on Objects section above.


You can also use the utility search method from the api:


```
from evennia.utils.search import search_tag
from evennia.utils.search import search_object
...
# search for tag "hangout" and categoy "location tags"
hangouts = search_tag(key="hangout", category="location tags")
wands = search_object("wand")

```


## Locks

https://www.evennia.com/docs/0.9.5/Locks.html


* make command only available only to the character holding it:

```
self.locks.add("call:holds()")
```

* restrict command to admins:

`perm(Admin)`

* make command available to all levels

`cmd:all()`

## Commands


```
self.caller - the game object calling the command
self.cmdstring - the command name used to trigger this command (allows
             you to know which alias was used, for example)
cmd.args - everything supplied to the command following the cmdstring
       (this is usually what is parsed in self.parse())
cmd.cmdset - the cmdset from which this command was matched (useful only
        seldomly, notably for help-type commands, to create dynamic
        help entries and lists)
cmd.obj - the object on which this command is defined. If a default command,
         this is usually the same as caller.
cmd.rawstring - the full raw string input, including any args and no parsing.
```


### Restricting commands to admin only

Wrap the command class with a new class that sets locks to `perm(Admin)`:

```
class CmdTimeHide(system.CmdTime):
    locks = "perm(Admin)"
```


In the `CharacterCmdSet` class' `at_cmdset_creation` method, add the new class like:

```
self.add(CmdTimeHide)
```

### Finding a target for the command

```
def func(self):
	"""Executes poke command"""
	target = self.caller.search(self.args.lstrip())
	if not target:
	    # we didn't find anyone, but search has already let the
	    # caller know. We'll just return, since we're done
	    return
	# we found a target! we'll do stuff to them.
	target.msg(f"{self.caller} pokes you.")
		self.caller.msg(f"You poke {target}.")
```



## Tags

### Properties

* `key` - name of tag
* `category` - 'None' if not specified
* `data` - meta

Tags are keyed by both the key and the category, so e.g. "chair" with no category, and "chair" with category "furniture" are two different tags.


### Adding / removing tags


You can tag any typeclassed object, namely Objects, Accounts, Scripts and Channels. General tags are added by the Taghandler. The tag handler is accessed as a property tags on the relevant entity:

```
mychair.tags.add("furniture")
mychair.tags.add("furniture", category="luxurious")
myroom.tags.add("dungeon#01")
myscript.tags.add("weather", category="climate")
myaccount.tags.add("guestaccount")

mychair.tags.all()  # returns a list of Tags
mychair.tags.remove("furniture")
mychair.tags.clear()
```
### Searching by tag

```
from evennia.utils.search import search_tag
...
# search for tag "hangout" and categoy "location tags"
hangouts = search_tag(key="hangout", category="location tags")
```


## Debugging


It can't be debugged via ide :(
Add the following to set the trace:

```
from evennia import set_trace;set_trace()
```

Then resart with

```
evennia istart
```

debugger commands:

```
l - list
p - inspect value
n - next
s - step into
c - continue
```

