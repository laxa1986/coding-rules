## Avoid using `final` keyword for variables/parameters

It doesn't bring any value, just adds noise to the code

## Use var keyword

Use var keyword in most scenarios especially when type is obvious from variable name like `request` or `user`.
Use an explicit type when it is very complex (custom type with generics)

## Do not use checked exceptions

There are pros and cons of checked exceptions, but in 202x the consensus is that they bring more noise than value.
Quick justification: even for unchecked exceptions, you need to write tests and thus provide guarantees that your code handles them
