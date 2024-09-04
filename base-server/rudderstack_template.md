Creating elasticsearch template for rudderstack JSON
-

Copy rudderstack_template.json and run command:

```bash
curl -X PUT -H 'Content-Type: application/json' -d @rudderstack_template.json http://localhost:9200/_template/rudderstack_template
```

OR use Kibana Dev Tool:

```
PUT _template/rudderstack_template
{
  "index_patterns": ["rudderstack-*"],
  "mappings": {
    "properties": {
      "message": {
        "type": "text"
      },
      "timestamp": {
        "type": "date",
        "format": "date_optional_time"
      }
    }
  }
}
```


Test request:

```bash
curl -X POST -H "Content-Type: application/json" -d '{"message": "Hello, Logstash!", "timestamp": "2025-03-05T06:44:53Z"}' http://localhost:8080/
```