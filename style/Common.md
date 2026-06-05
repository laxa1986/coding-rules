# Common rules

Common rules for programming languages such as Java, JavaScript/TypeScript, Python, Go.
Not applicable to DSL languages such as SQL, HCL (Terraform), XML, YAML and so on

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

## Negative conditions at the top

Place negative conditions at the top of the method, doing either return or throw slowly progressing to happy path:

```jupyter
def delete(self, key: str) -> DeleteEnvResult:
    # check that such env exists and throw if not

    # handle special case - environment is already being - return a well formed response

    # happy path - environment exists and is not being deleted - do the actual deletion
    _delete()
```

## Short circuit

This rule is a evolution of `Negative conditions at the top` rule. Idea is that if there are 2 positive/neutral branches,
but one of them is much smaller than another, then it is better to short circuit the smaller one
and return early instead of nesting main logic in `else` block

```java
if (condition) {
    // small block
} else {
    // main logic
}
```
Fix
```java
if (condition) {
    // small block
    return;
}
// main logic
```

## Method order

Given method a calls method b, e.g.:
```java
void a() {
    b();
}
```
Method `a` must be placed higher in the code than method `b`

## Return empty collection, not null

Given method signature that returns a collection, e.g.
```
List<String> getNames() // java
getNames(): String[] // javascript
def get_names() -> List[str] # python
```
If there are no names to return, it should return an empty collection instead of null/None

## Annotations in staircase style

Many languages allow placing some metadata on classes and methods in the form of annotations/decorators.
Idea is to place them in a staircase style:

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


## Line length

Code needs to be compact, require less scrolling. Modern monitors make comfortable to have up to 150 characters.
Do not wrap lines at 80 not even 120 characters as long as it fits 150.
```java
// do not wrap unless it exceeds 150 characters
var res = someService.longMethodName(param1, ... paramN)

// if the line is too long because of calling other functions
var res = someService.longMethodName(param1, calculateParam2())
// then instead of wrapping the line, move the calculation of param2 to a separate line
var param2 = calculateParam2();
var res = someService.longMethodName(param1, param2);
```

## Quotes for strings

Some programming languages have two types of quotes for strings: single and double: JavaScript/TypeScript, Python.
Use single quotes everywhere for consistency
