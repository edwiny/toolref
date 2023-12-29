# Splunk usage notes



## gotchas

### Quoting


Single quotes indicate fields, double quotes indicate string values.

E.g.

```
| eval successes=if('migration.status' = "SUCCESS" and 'migration.checkpoint' = "VERIFY", 1, 0)
```

## Example queries



* Get distribution over time of the occurances of a log message


```
message="*password authentication failed*" level=ERROR | bucket _time span=1h | stats count by _time
```

* Search all log entries that contain a certain field

```
eventtype="err.msg"="*"
```

* Search by regex on a field

```
regex hostname="atlassian.net"
```

* Parse a field out of a text message:


```
"Server startup in" | rex field=message "Server startup in (?<startup>.*) ms" | eval startupsec=startup/1000 | stats min(startupsec), max(startupsec), avg(startupsec)
```

* Calc percentiles

```
message="Access Log" fields.backend=confluence-prod-us-1-* fields.host=* | timechart median(fields.duration_s) as median p95(fields.duration_s) as "95th percentile" p99(fields.duration_s) as "99th percentile" 
```


* Get list of urls that 5xx the most

```
nginx status=5** | rex field=uri_path "^(?<uri>.*)\?" | timechart span=1m count by uri
```

* Truncating time from date field and charting results

```
eval date=strftime(_time, "%Y-%m-%d") | 
stats sum(wfduration) as shardduration by product, metric.tags.parentTaskId, metric.tags.shard, date |
stats max(shardduration) as deploy_duration by product, metric.tags.parentTaskId, date | 
chart p90(deploy_duration) over date by product
```

NOTE: you can also just modify _time and let timechart use that:
```
... | eval _time = strftime('field.name', <format>) | timechart count
```

NOTE: or better, use 'bin':

```
.. | bin span=1d | timechart p90(deploy_duration) by product
```

* Finding earliest (or latest) time in a sample

```
stats earliest(_time) as earliest |
eval earliest = strftime(earliest, "%Y-%m-%d:%H:%M")
```


* building a string from fields

```
 eval ddurl="https://app.datadoghq.com/monitors/" + 'events.metadata.request.monitorId' | 
```


 * count number of items based on condition

```
 | stats count(eval('events.metadata.anomalyDetected'=="true")) as num_anomalies by events.tags.version
```
 

## Time

`time`: the time emitted by application
`_time`: UTC timestamp. Internal value. Built from 'time' in logs. 

`... | eval indextime = _indextime`   - the time it was indexed by Splunk



### Getting earliest and latest

```
stats earliest(_time) AS Earliest, latest(_time) AS Latest | eval LastEvent=strftime(Latest,"%+")
```


## Operators


AND is implied
Can use OR , NOT

MUST use capitals!


## Commands


### Streaming vs non-streaming

Streaming operates on each event, non-streaming requires the events from all of the indexers before the command can operate on the entire set of events.

E.g. streaming: eval
non-streaming: dedup, top, sort


### Distributed Streaming commands

Can run in parallel, just adds fields to each stream e.g.
eval, fields, rename, regex, replace, strcat, where


* eval 

creates a new field or replace existing fields based on eval functions


### Centralised Streaming commands

Must run on the final result set aka "search head". E.g:
head, streamstats, some modes of dedup, 

### Generating commands

Does not require any input e.g. tstats, search, pivot


### Transforming commands

Orders results into a data table.
Unlocks the Statistic tab


* top [limit=n]
use limit 1 to find the top item and use it as input to another function

* stats
Does SQL-type aggregation. Without BY clause returns a single result aggregated over entire result set. If you use a by clause one row is returned for each distinct value specified in the by clause.


Can use the Statistical and Charting functions:
* count / c (field)
*  dc (field)  - distinct values only
*  max, min, avg, median, p (percentile), sum

time functions
*  per_day
*  per_rour
*  per_minute




