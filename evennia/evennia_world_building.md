# Evennia world building notes

## Creating objects


```
create box; a wooden box
describe box = description of box
drop box

# OR

create/drop box; a wooden box
```

Creating something from a custom script:

```
create/drop button:tutorial_examples.red_button.RedButton
create wand:hogwarts_objects.Wand
```

Finding objects:

```
find button
```

Moving objects around:

```
teleport box = house
```
Knowing the dbref:

```
teleport #8 = here

```

Destroying object:

```
destroy box
```


## Pinning objects to rooms

```
lock box = get:false()
set box/get_err_msg = It's way too heavy for you to lift.
```

## Deleting objects

Need to identify objects by their 'dbrefs'.

```
destroy 2
destroy 10-12
```

Can inspect objects via the admin web interface

http://localhost:4001/admin/objects/objectdb/?o=1.3.4




## Room building

```
describe here = a description for the room
```

Create a room:

```
tunnel sw = cliff
```

* Creates a new room 'cliff'
* Creates exists 'sw' in current room and 'ne' in new room

Create exists:


```
open north;n = house
```


## Text formatting


Add red colour:

```
describe here = |rFire!|n There is a fire here
```

|/ - newline

