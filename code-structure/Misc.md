# Common rules

## Return empty collection, not null

Given method signature that returns a collection, e.g.
```
List<String> getNames() // java
getNames(): String[] // javascript
def get_names() -> List[str] # python
```
If there are no names to return, it should return an empty collection instead of null/None

## public vs private

Use the most restrictive access modifier possible. If a method / field is used only within the class, make it private.
If it is used in the package, make it package-private (java), protected (python).
Only if it is used outside the package, make it public.

Note: do NOT make a method public just because you want to write a unit test for it.
If a method is not used outside the class, it should be private, even if it makes testing harder.

## Dependency injection

Use DI. It makes code modular, testable, and easier to maintain

## Logic in domain models

There is a practice that advocates putting business logic in domain models (entities).
I've seen it once, and it was quite a bad experience because now you constantly need to keep in mind
what logic to keep in services and what in domain models.
Good bulletproof practice is to keep logic in services and keep models and dumb data structures.
So you completely exclude the 'model' package from test coverage (just one good side effect)

## Inheritance

Try not to use inheritance. Or rather limit inheritance to just one level: interface and implementation.
Of course, there are exceptions, but they should be very limited.
The main problem with inheritance is that it makes code harder to understand and maintain

## Logging context

Java MDC, ... 
