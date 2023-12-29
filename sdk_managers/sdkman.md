# SDK Man usage notes


## Finding list of sdks installed in local env

```
sdk list java | grep installed
               | >>> | 8.0.372      | amzn    | installed  | 8.0.372-amzn
               |     | 17.0.7       | tem     | installed  | 17.0.7-tem
               |     | 11.0.19      | tem     | installed  | 11.0.19-tem
```

## Switching to another version

```
# make it default across all shells
sdk default java 17.0.7-tem

# or only current shell
sdk use java 17.0.7-tem
```


## Seeing which one is installed

```
sdk current java

Using java version 17.0.7-tem
```

