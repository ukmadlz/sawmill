# Sawmill
Log enricher an transformer TBD

Simple configuration example:
```json
{
  "steps": [
    {
      "grok": {
        "name": "grok message",
        "config": {
          "field": "message",
          "overwrite": [
            "message"
          ],
          "patterns": [
            "(%{IPORHOST:client_ip}|-) %{USER:ident} %{USER:auth} \\[%{HTTPDATE:timestamp}\\] \\\"(?:%{WORD:verb} %{NOTSPACE:request}(?: HTTP/%{NUMBER:httpversion})?|%{DATA:rawrequest})\\\" %{NUMBER:response:int} (?:%{NUMBER:bytes:float}|-) B %{DATA:thread} %{NUMBER:response_time:float} ms %{DATA:servername} %{DATA:client_id:int}(\\;%{NOTSPACE})? %{DATA:device_id} %{DATA}"
          ]
        }
      }
    },
    {
      "removeField": {
        "name": "remove message field after grok",
        "config": {
          "path": "message"
        }
      }
    }
  ]
}
```

## Processors


Processors:
- Add Field [addField]
	- path (the path to the field to add, doted fqdn) 
	- value 
- Append List [appendList]
	- path (the path to the field to add, doted fqdn) 
	- values - array of values to add, i.e. values: ["val1","val2"]
- Add Tag [addTag]
	- tags - array of tags to add, i.e. tags: ["tag1","tag2"]
- Convert Field [convert]
	- path
	- type (one of: long, double, string, boolean)
- Date [date]
	- field 
	- targetField
	- formats - An array,  one of these: https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html
	- timeZone - one of these: https://docs.oracle.com/javase/8/docs/api/java/time/ZoneId.html
   Example:
   ```
   {
      "date": {
        "config": {
          "field": "timestamp",
          "targetField": "timestamp",
          "formats": [
            "ISO8601"
          ]
        }
      }
    }
    ```
	
- Drop [drop]
	- percentage, default to 100 which is full drop, can be used to throttle
- Geo IP [geoIp]
	- sourceField
	- targetField
	- properties
	- tagsOnSuccess
- Json [json]
	- field
	- targetField
- Key Value [kv]
	- field
	- targetField
	- includeKeys
	- excludeKeys
	- trim
	- trimKey
- Remove Field [removeField]
	- path (dotted path, i.e: a.b.c)
   Example:
   ```
   {
      "removeField": {
        "config": {
          "path": "timestamp"
        }
      }
    }
    ```
- Remove Tag [removeTag]
	- tags - list of tags
- Rename Field [rename]
	- from - the field name to rename
	- to - the new name of that field
- Substitue [gsub]
	- field
	- pattern
	- replacement
- User Agent [userAgent]
	- field
	- targetField
	- prefix
  
## Conditions
- in
	- path
	- value
- hasValue
	- field
	- possibleValues
- matchRegex
	- field
	- pattern
	- caseInsensitive - default false
	- matchPartOfValue - default false
- exists
	- field
	
## Additional Commands
- stopOnFailure 
    - false (default)  The pipeline will continue through the steps even if there is a processor failure
    - true - The pipeline will stop processing at the first processor that has a failure
    
	

## Open source
In order to move to public repository, these are the items we need to fix:
- Remove the dependency of custom java-grok in Nexus
