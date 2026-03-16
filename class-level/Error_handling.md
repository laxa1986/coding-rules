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
If there are multiple lines that can raise an error, then they should be in separate `try` blocks

## Rule 2. No log and throw

Most of the time unhandled error is logged by the generic error handler, by framework, or by the runtime itself.
Most of the time such generic handler logs the error. So we come to double logging (and double stack trace!)


## Rule 3. Rethrowing exceptions

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