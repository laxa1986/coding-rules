# Unit tests

Each test has three parts: Arrange, Act and Assert.
Arrange is when you prepare your test data and mock dependencies.
Act is when you call the method under test.
Assert is when you check that the result is what you expected.

## No asserts before Act

Some people do arrange and place some asserts to ensure arrangement is correct.
Perhaps you can do it, but do not leave such asserts in the final code

