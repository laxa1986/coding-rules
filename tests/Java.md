# Java

## use any()

Don't use `anyString()`, `anyList()`, unless there are overloaded functions, so you need to be specific. 
Use just `any()` - it makes tests more readable and less verbose

## Do not verify a mock call if it has special setup

```java
// Mockito STRICT_STUBS (default) already ensures this stub is used
when(repository.find()).thenReturn(result);

service.call();

// Redundant unless you want to verify explicit number of invocations
verify(repository).find();
```
