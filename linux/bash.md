# bash notes

* test var for emptiness

```
if [ -z "$ARG" ]; then
echo "its empty";
fi
```

* getopt example

From: https://wiki.bash-hackers.org/howto/getopts_tutorial

```
#!/bin/bash
 
while getopts ":a:" opt; do
  case $opt in
    a)
      echo "-a was triggered, Parameter: $OPTARG" >&2
      ;;
    \?)
      echo "Invalid option: -$OPTARG" >&2
      exit 1
      ;;
    :)
      echo "Option -$OPTARG requires an argument." >&2
      exit 1
      ;;
  esac
done

shift $((OPTIND-1))
LAST_ARGUMENT=$1

```



* getting the directory from where a script is executing

```
script_dir="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"

source $script_dir/config.sh
```
