## Rule 1. No HTTP statuses in business logic

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

## Rule 2. No HTTP request/headers in service layer

Do not pass HTTP request or headers to the service layer.
Request/headers validation should take place in the controller layer

```pyhon
class UserService
    def get_user(request):
        user_id = request.headers.get('X-User-Id')
        return user_dao.get_user(user_id)
```

Fix:

```pyhon
class UserService
    def get_user(user_id):
        return user_dao.get_user(user_id)
```
