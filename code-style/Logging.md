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
