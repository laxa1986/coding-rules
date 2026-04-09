# Three Level architecture

This is the most important concept you need to follow while writing a software code

Idea: you have three levels: controller, service, repository. Do it even for simple applications.
Remember – simple applications tend to grow and get bigger. With 3-level architecture you make your application modular,
easy to scale and extend. This is uber important when working as a team.

More specifically, these three levels are:
1. API level how your system gets called – most often it is a REST API or GraphQL.
In the case of event-driven systems, it can be a Kafka listener or any other message broker.
It can be a Lambda layer handling API GW events. Basically this is your interface to the world.
2. Service level is also often called Core. Here you can relax and forget any specific technology and operate via business models,
write your business code. It should be super easy to write unit tests for this code and crucial to do a thorough code review!
You can expect most of your code to be at this level
3. Downstream communication. Most often it is a Data Base or other APIs (REST/GraphQL).
Here you forget about business logic, your goal is to be dumb and blind about business,
make effective communication – modern ORM tool for DB, robust API client for synchronous downstream systems

Each level operate with its own data classes: DTOs for Controller level,
Domain Models for Service level and Entities for DB or another set of DTOs for a downstream system,
consider table along with classes you may expect to meet on each level

| API Level         | Service Level  | Downstream Level  |
|-------------------|----------------|-------------------|
| ProductController | ProductService | ProductRepository |
| ProductDto        | Product        | ProductEntity     |

Also, if you have three types of data, guess what? You need converters! Often people think – oh no,
this is too much, wouldn't it be easier to just have one Controller and put everything in it?
Let’s look deeper into the responsibilities of each level to understand how important to have each of them

## API level

Receive a call from outside, validate the request at a high level, call service level,
and then transform service level response into format applicable for this API

1. This level is _a VERY_ technology-specific level. You might expect to have `@RestController` or `@KafkaListener` on classes in API level 
2. Data classes on this level also technology specific: you might have `@JsonPropery` to control how fields are named in JSON representation
3. Often your domain model needs to be transformed into an API-specific format. Like you have java `Money`,
but in JSON it needs to be two fields `amount` and `currency`. Or you have certain fields that should not be exposed
4. At this level you verify that request is syntactically correct: field `accountNumber` present and has exactly 12 digits.
But you do not check if such an account really exists and belongs to the current user – this is work of Service level!
5. At this level you can work with http headers (or event metadata in case of asynchronous service),
particularly you can do Authorization – often endpoints annotated with `@RequireScope("operations")`
that mean - get request header `Authorization`, take JWT scopes and check if there is a scope "Operations" in it

As you can see, there are lots of responsibilities on this level, so it deserves to be separated

## Service level

This is the level where all business logic belongs to. Here you create your products, fulfill orders,
notify customers about delivery and so on. Here are all checks that email is unique,
that the customer has a positive balance, that warehouse has necessary supplies etc

1. Here classes are often having suffix `Service` and have Spring annotation `@Service`
2. Data classes are often annotated with lombok `@Data` annotation and fields are full of `@NotNull` or `@NotEmpty` annotations
3. Often you can see other JSR303 annotations from `jakarta.validation.constraints` package like `@Email`, `@Pattern`, `@Min`
which are wonderful tools to describe your business declaratively (no need to do `if (email really looks like email)`)  
4. Here you have your business exceptions like `InsufficientFundsException` or `EmailAlreadyExists`
5. Here you can do your fine-grained authorization: when user authorized to access `GET /orders/{id}`
endpoint actually tries to get access to another user order. Note – you can't make this check on the Controller level.
In this case you'll throw `UnauthorizedDataAccessException` which is likely will be mapped into `403` status code 
6. And what is the most fruity about this level – you can write unit tests!
You do not care how data came in and don't care how downstream works – you just mock them! 

Note: if you end up with one Service using another Service - it is a good idea to call higher level one a Facade.
Facades are also part of the Service / Core level

## Downstream level

This level is similar to the API level in the sense that it is also technology-heavy.
Hexagonal model doesn't even distinguish them, they are both _adapters_, but more about it in another article

1. For level communicating with DB you expect to see classes with suffix `Repository` and Spring Data annotations like `@Repository`.
Classes often have annotations `@Entity` and `@Table`
2. When you want to call a synchronous downstream system do expect to see any sort of
`HttpClient`, `FeignClient`, `RestTemplate` + bunch of serialization, work with headers etc
3. Typically, here you can have code that mimics network communication to hide it from Service level such as network timeouts,
retries, circuit breaker, handling of special http codes like 400 -> `Optional.empty()` etc
4. Authorization. To call downstream, you often need to identify yourself, so be prepared to generate some sort of token
before a call or any other authentication mechanism

All these examples aim to build an understanding that each level is pretty heavy itself to merge two in one,
and thus you need all of them even if your project is super small CRUD API over relational DB
