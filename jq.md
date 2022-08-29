
# jq notes

tl;dr:

the .[] notation represents the keys (or array indices) to select from the input, so .[0,1] would select the first 2 array elements and .['foo'] would select the object with key foo




`.["foo"]` is equivalent to `.foo`

The [] notation works as object key lookup as well as array index.

The | takes output of prev filter and feed it as input to next filter.

`.a.b.c` is the same as `.a | .b | .c`.



## Get single value 

given input

```
{
    "resource_guid": "fff843df-b1bf-48e9-a186-9a39217726f4",
    "user_conn_limit": 100
}
```

query:

```
 jq .user_conn_limit
```


returns

```
100
```


given input

```
{
    "instance": {
        "aws_status": "available",
        "name": "blah"
    }
}
```

query:

```
jq .instance.aws_status
```

returns


```
"available"
```

* Get value from a array of objects

Data:

```
[
    {
        "created": 1522235348,
        "handle": {
            "id": "33747489",
        }
        "status": "success"
    },

...
]

```


Query:

```
    | jq '.[] | .handle.id'

```

outputs

```
"33747489"
"c4f33815"
"5807a0c1"
"c91e9cbd"
"0d13b137"
```


## Create list of smaller objects from list of bigger objects

same input as above

```
| jq '.[] | { id: .handle.id, instance: .handle.instance_id }'
```

outputs

```
{
  "id": "78418e9a",
  "instance": "001"
}
{
  "id": "b0eb5796",
  "instance": "002"
}
{

  "id": "14c1d2a0",
  "instance": "002"
}
```



## return a array element where one of the keys have a certain value

Input:

```
{
  "shard1": [
    {
      "Shard": "shard1",
      "Current": 0,
      "Override": false
    },
    {
      "Shard": "shard1",
      "Current": 1,
      "Override": false
    },
    {
      "Shard": "shard1",
      "Current": 2,
      "Override": true
    },
  ]
}
```

Query: 

```
| jq '.[] | .[] | select(.Override==true)'
```

Output:

```
{
  "Shard": "shard1"
  "Current": 2,
  "Override": true
}
```

## print out the keys from a dictionary

input:

```
{
  "shard1": {
    "capacity": {
      "autoscale_enabled": false
    }
  },
  "shard2": {
    "capacity": {
      "autoscale_enabled": false
    }
  }
}


$ cat /tmp/bla | jq keys
[
  "shard1",
  "shard2"
]
```

to return as list without quotes or commas:


```
$ cat poollist.json  | jq  -r 'keys | .[]'
```

## pass arguments into the jq expression


```
jq --arg rds $RDS --arg key $KEY '.[].instances | .[] | select(.dbguid==$rds) | .[$key]'
```


### operators

https://stedolan.github.io/jq/manual/#Builtinoperatorsandfunctions





