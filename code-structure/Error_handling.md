# Error Handling


## Rule 1: Single Line in a try block

If there are multiple lines in a try block, then catch block cannot be sure which line raised an error

Example:
```javascript
try {
    const url = 'https://api.example.com/' + path
    const response = await fetch(url, options)
    return await response.json()
} catch (error) {
    throw new Error('Network error')
}
```
Here error can be raised not only by `fetch`.
If error comes from `response.json()`, then throwing `Network error` would be misleading

*Fix*: everything that cannot raise any error – move out of `try` block (like url preparation).
If there are multiple lines that can raise an error, then they should be in separate `try` blocks,
EVEN if it requires to move some variable definition out of the `try` block:

```javascript
const url = 'https://api.example.com/' + path
let response
try {
    response = await fetch(url, options)
} catch (error) {
    throw new Error('Network error')
}
try {
    return await response.json()
} catch (error) {
    throw new Error('response body parsing error')
}
```

This code does look more verbose, but it is more accurate and easier to debug.
You can easily understand which line raised an error just by looking at the error message.

## Rule 2. Rethrowing exceptions

Often you need to rethrow an exception: sometimes keep same type and just add some context, sometimes wrap exception in another one.
Do it right! Do not concatenate exception to a message, almost all languages give a mechanism of exception chaining, use it!

Java
```java
} catch (OriginalException e) {
  throw new NewException("Message", e);
}
```

JavaScript
```javascript
} catch (error) {
  throw new NewException("Message", { cause: error });
}
```

Python
```python
except OriginalException as ex:
    raise NewException('Message') from ex
```

Go
```go
func GetAccount(ctx context.Context, dynamo *dynamodb.Client, custId int) (model.Account, error) {
	var acc model.Account
	resp, err := dynamo.Query(ctx, &input)
	if err != nil {
		return acc, fmt.Errorf("dynamodb query: %w", err)
	}
}
```

## Rule 3. No log and rethrow

Most of the time unhandled error is logged by the generic error handler, by framework, or by the runtime itself.
Most of the time such generic handler logs the error.
So if you catch error, log it and re-throw, then you end up with double logging (and double stack trace!)

Java
```java
} catch (IOException e) {
    LOGGER.error("message", e); // <-- this is redundant
    throw new ServiceException("message", e);
}
```

## Rule 4. Log exception

On the other side, if you write a piece of code where exception is finally handled,
them make sure you DO log it

```java
} catch (IOException e) {
    LOGGER.error("message", e);
}
```

In python if you catch an exception there is always 'current exception' `sys.exc_info()`,
so you might not need to explicitly pass it, just call right method (exception, not error) to log it:
```python
except Exception:
    logger.exception('Internal server error')
```
