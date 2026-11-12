## Avoid including code location in log messages

When logging an error, do not include the code location in the log message.
It should be done at logger level.

Bad:
```java
class InvoiceService {
    private static final Logger logger = LoggerFactory.getLogger(InvoiceService.class);
    ...
    void method() {
        logger.info("[InvoiceService] Creating invoice for order {}", orderId);
    }   
}
```

Good:
```java
class InvoiceService {
    private static final Logger logger = LoggerFactory.getLogger(InvoiceService.class);
    ...
    void method() {
        logger.info("Creating invoice for order {}", orderId);
    }
}
```

## Values in fields, message text fixed

In JSON logs (CloudWatch and alike) put values you may query by - durations, counts, ids, exit codes - in separate fields,
keep the message text fixed. Then one query covers all records of a kind: filter, aggregate and sort by the value,
no parsing of message text. Logs stay readable: the console format prints the fields next to the message.

Short readable names stay in the text, they make the line easy to scan (ex: "send env.deleted event to event bus",
"process started: ./tf-state-pull.sh"). So does a reason meant to be read as a sentence (ex: "terraform destroy failed:
lack of permissions: ..."). Never put an object in a field: it is written with all its fields (credentials too),
use its safe `toString()` in the message instead.

Java (logback + logstash-logback-encoder): `Markers.append(name, value)` adds a field to this record only
(`MDC` and context properties add fields to all following records). A small helper keeps call sites short:

```java
@UtilityClass
public class LogMarkers {
    public static LogstashMarker durationSince(long startNanos) {
        return Markers.append("durationMs", Duration.ofNanos(System.nanoTime() - startNanos).toMillis());
    }

    public static LogstashMarker field(String name, Object value) {
        return Markers.append(name, value);
    }
}
```

Bad:
```java
log.info("Process {} successfully finished in {} ms", pid, durationMs);
log.info("removed {} datasources", count);
```

Good (static imports of `LogMarkers` methods; several fields: `field(..).and(field(..))`):
```java
log.info(durationSince(startNanos).and(field("pid", pid)), "process finished with exit code 0");
log.info(field("count", count), "datasources removed");
```

JSON record:
```json
{"level": "INFO", "message": "process finished with exit code 0", "pid": 35, "durationMs": 5760, "requestId": "d12db081", ...}
```

Python (standard logging with a JSON formatter): `logger.info('event handled ok', extra={'durationMs': 512})`

Queries this enables (CloudWatch Logs Insights), w/o parsing message text:

```
# slowest steps across all runs of the last week
filter ispresent(durationMs)
| stats avg(durationMs) as avgMs, max(durationMs) as maxMs, count(*) as runs by message
| sort avgMs desc

# runs where downloading the aws provider took longer than 30 sec
filter message = "install aws provider done" and durationMs > 30000
| fields @timestamp, requestId, durationMs

# how the number of removed datasources is distributed
filter message = "datasources removed"
| stats min(count), avg(count), max(count)
```
