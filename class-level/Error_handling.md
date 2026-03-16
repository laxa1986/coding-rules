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

## Rule 4. No HTTP statuses in business logic

Given you have 3 layer architecture:
- http - service - DB
You should not throw BadRequest/InternalServerError from the service layer!

Fix:
- 400 – any bad request should be validated at the controller level.
What if you register a new user and email is already taken, and you only know about it in the service layer?
Well, it is not a 400 anymore, it is a 422 business exception
- 401 – ideally should be handled by API Gateway. But if authentication logic is in application code,
then it should be in the generic authentication filter, not in the service layer.
How about a downstream service that returns 401? If you use pass-through authentication,
then perhaps returning 401 from the service layer can be legit.
But if you use a dedicated service account to call that downstream system – then it is 500
- 403 – this is a legit exception, say, user authorized to call a given endpoint,
but requested a data that belongs to another user.
But still, it is better not to throw `UnauthorizedException`,
it's better to forge dedicated `UnauthorizedDataAccessException` and then map it to 403 in controller layer
- 422 – design a `BusinessException` and map it to 422 in controller layer.
The beauty of this exception is that it can pass a message as is to the client
- 500 - do not throw `InternalServerError`. You either do not catch original exception (such as NPE) 
and let it be handled by the generic error handler,
or you catch original one and wrap in generic `RuntimeException` (java), `Error` (javascript), `Exception` (python)
- 503 - sometimes unavailable downstream system is a reason to return 503
