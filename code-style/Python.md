# Python

## Do not use Optional

For parameters and return types, do not use `Optional[X]`. Use `X | None` instead.
`Optional[X]` is an old syntax (prior to Python 3.10). It is a little more verbose and requires importing `Optional` from `typing`
