# Evennia world building notes

## Quality of life

to connect use 

```
rlwrap telnet localhost 4000

```

connecting via web socket: localhost:4001/webclient/
	



## Objects


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


### Pinning objects to rooms

```
lock box = get:false()
set box/get_err_msg = It's way too heavy for you to lift.
```

### Deleting objects

Need to identify objects by their 'dbrefs'.

```
destroy 2
destroy 10-12
```

Can inspect objects via the admin web interface

http://localhost:4001/admin/objects/objectdb/?o=1.3.4

### Set object attributes

```
set #44/is_open = False
```



## Room building

```
describe here = a description for the room
```

### dig

```
    dig[/switches] <roomname>[;alias;alias…][:typeclass]
        [= <exit_to_there>[;alias][:typeclass]]
            [, <exit_to_here>[;alias][:typeclass]]

```

Create a new room with no exits leading to it:


```
dig/teleport The Warehouse
```



### Tunnel

tunnel is when you want to create rooms with exists using compass directions or up/down

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


## Various

Stepping down from godhood: `quell`


## Troubleshooting

`examine` - examine internal details of object


