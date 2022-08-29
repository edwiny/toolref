# Curl notes


## POSTing a JSON object to a webservice


```
curl --request POST \
     --header "Content-Type: application/json" 
     --data '@mydata.json' 
     $URL

```
