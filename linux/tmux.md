# tmux notes


Split terminal screens and restore ressions

### cmd sequences

hold and release Ctrl-b, followed by the command you want to send

### starting up

```
tmux
```

### splitting screen horizontally

```
C-b %
```

### splitting screen veritcally

```
C-b "
```

### switching between windows

```
C-b arrow key
```

### creating new screen

```
C-b c
```

### navigating *screens*

```
C-b p  for prev
C-b n  for next
C-b number use number displayed in status bar at the bottom
```

### detaching from session

```
C-b d
```

### re-attaching to sessoins

```
tmux ls

tmux attach -t 0     # 0 is from the ls output

```

### naming sessions

can create with name like this:

```
tmux new -s my_session_name

# or rename:


tmux rename-session -t 0 database

```

### scolling up through history

```
C-b [
```
