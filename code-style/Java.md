## Avoid using `final` keyword for variables/parameters

It doesn't bring any value, just adds noise to the code

## Use var keyword

Use var keyword in most scenarios especially when type is obvious from variable name like `request` or `user`.
Use an explicit type when it is very complex (custom type with generics)

## Do not use checked exceptions

There are pros and cons of checked exceptions, but in 202x the consensus is that they bring more noise than value.
Quick justification: even for unchecked exceptions, you need to write tests and thus provide guarantees that your code handles them

## .equals and .hashCode

Only override `equals` if there is a real need to compare objects (like `contains` or `indexOf`).
This is quite rare, so make sure you do not override `equals` for any DTO/Entity class

Only override `hashCode` if your class is used as a key in a hash-based collection (like `HashMap` or `HashSet`).
This is extremely rare, do not override `hashCode` just because you override `equals`

## Lombok @Data

Lombok's `@Data` annotation generates `equals` and `hashCode` methods by default. Most of the time you do not need them.
So _DO NOT_ use `@Data`, use class level `@Setter`, `@Getter` instead
