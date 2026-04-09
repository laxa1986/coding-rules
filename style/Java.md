## Annotations in staircase style

## Avoid using `final` keyword for variables/parameters

It doesn't bring any value, just adds noise to the code

## Use var keyword

Use var keyword when type is obvious from variable name like `request` or `user`

## Annotations in staircase style

```java
@Service
@Slf4j
public class MyClass { }
```

Fix:
```java
@Slf4j
@Service
public class MyClass { }
```
