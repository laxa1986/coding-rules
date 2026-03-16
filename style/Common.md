# Common rules

## Code reputation

Many tools like SonarQube do detect code duplication blocks. Here we talk about more subtle cases like this:

```java
if (!request.getRecipient().getAddress().getCountry().equals("US")) {
    throw new IllegalArgumentException("Unsupported country: " + request.getRecipient().getAddress().getCountry());
}
```
Fix:
```java
String country = request.getRecipient().getAddress().getCountry();
if (!country.equals("US")) {
    throw new IllegalArgumentException("Unsupported country: " + country);
}
```

## Short circuit

```java
if (bad condition) {
    // small block
} else {
    // main logic
}
```
Fix
```java
if (bad condition) {
    // small block
    return;
}
// main logic
```