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

## Line length

Code needs to be compact, require less scrolling. Modern monitors make comfortable to have up to 150 characters.
Do not wrap lines at 80 not even 120 characters as long as it fits 150.
```java
// do not wrap unless it exceeds 150 characters
var res = someService.longMethodName(param1, ... paramN)

// if line is too long because of calling other functions
var res = someService.longMethodName(param1, calculateParam2())
// then instead of wrapping line, move calculation of param2 to a separate line
var param2 = calculateParam2();
var res = someService.longMethodName(param1, param2);
```

## Quotes for strings

Some programming languages have two types of quotes for strings: single and double: JavaScript/TypeScript, Python.
Use single quotes everywhere for consistency
