# Unit tests

Each test has three parts: Arrange, Act and Assert.
Arrange is when you prepare your test data and mock dependencies.
Act is when you call the method under test.
Assert is when you check that the result is what you expected.

## No asserts before Act

Some people do arrange and place some asserts to ensure arrangement is correct.
Perhaps you can do it, but do not leave such asserts in the final code

## Nested tests

## current time

TODO: example in Java, TypeScript and Python

## Avoid filesystem

Often you process some files and need to compare before and after.
First though it is place test files in `src/test/resources` and then read them in your tests.
Main problem is that when you look into a certain test `shouldTolerateBrokenData` - you loose context.
It is better to place small test data right in the test code (using multi line string literal)
