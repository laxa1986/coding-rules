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

Use most restrictive access modifier possible. If method/field is used only within the class, make it private.
If it is used in the package, make it package-private (java), protected (python).
Only if it is used outside the package, make it public.

Note: do NOT make method public just because you want to write a unit test for it.
If method is not used outside the class, it should be private, even if it makes testing harder.
