# REST Guidelines

The following are guidelines are the industry best practices for REST services. Your API doesn't need to follow all of these guidelines to be functional, but failing to follow too many guidelines will result in a REST API that is confusing and hard to consume.

**Page Contents**

- [Terminology](#terminology)
- [Examples Background](#examples-background)
- [API Request Inputs](#api-request-inputs)
- [API Response Outputs](#api-response-outputs)
- [HTTP Methods](#http-methods)
    - [GET](#get)
    - [POST](#post)
    - [PUT](#put)
    - [DELETE](#delete)
- [Versioning](#versioning)

## Terminology

#### API

An interface that accepts inputs and produces associated outputs. For example, an API that adds two numbers and returns the result could accept the inputs `2` and `3` and would output `5`. REST API's provided a standardized API interface for interacting with web resources.

#### Collection

Multiple [resources](#resource) together. For example a `Person` can be a resource and the collection for this resource would be a list of `Person` resources.

*[RESTful API Designing Guidelines](https://hackernoon.com/restful-api-designing-guidelines-the-best-practices-60e1d954e7c9)*

#### Endpoint

A location within the REST API (specified by domain, path, and [method](#http-methods)) that is used to retrieve or affect resources.

*[Wikipedia Web API](https://en.wikipedia.org/wiki/Web_API)*

*[RESTful API Designing Guidelines](https://hackernoon.com/restful-api-designing-guidelines-the-best-practices-60e1d954e7c9)*

#### Idempotent

An [endpoint](#endpoint) is considered idempotent if multiple requests with the same input will have the same effect on the resource as that of a single request. For example, deleting a resource is considered idempotent and telling it to delete again will still result in the resource being deleted.

*[IETF rfc7231 Idempotent](https://tools.ietf.org/html/rfc7231#section-4.2.2)*

#### Resource

A set of related data. For example, a `Person` resource could include related data such as `first name`, `last name` and `birthdate`.

*[IETF rfc7231 Resources](https://tools.ietf.org/html/rfc7231#section-2)*

*[Wikipedia Web API](https://en.wikipedia.org/wiki/Web_API)*

*[RESTful API Designing Guidelines](https://hackernoon.com/restful-api-designing-guidelines-the-best-practices-60e1d954e7c9)*

#### Stateless

REST is stateless. Each request must contain all of the data necessary to perform the request. The server does not store any information about the client session.

*[RESTful API: Statelessness](https://restfulapi.net/statelessness/)*

*[IETF rfc7231 Abstract](https://tools.ietf.org/html/rfc7231)*

## Examples Background

For the examples contained within this document we'll be using:

1) A fictitious database table that stores email addresses

    | id  | email |
    | --- | ----- |
    | 1 | bob@fake.net |
    | 2 | amanda@fake.net |
    | 3 | chris@fake.net |

2) A fictitious REST API with the following endpoints:

    | Endpoint | Description |
    | -------- | ----------- |
    | `GET /` | Gets a list of email addresses. |
    | `POST /` | Stores a new email addresses. |
    | `GET /{id}` | Gets the email address associated with the ID. |
    | `PUT /{id}` | Sets the email address with the associated ID. |
    | `DELETE /{id}` | Deletes the email address and its associated ID. |

3) A fictitious domain: `http://email-registry.com`

## API Request Inputs

There are five ways to provide input to an API:

1. The method, discussed in detail below in the [HTTP Methods section](#http-methods).

2. The path.

    - The path is part of the endpoint identifier.
    
    - A path may be static or it may contain path parameters (i.e. variables).
    
    - A static path never changes. Using the [examples](#examples-background), these would include `GET /` and `POST /` (where `GET` and `POST` are the methods).
    
    - A path with parameters accepts variables. Using the [examples](#examples-background), these would include `GET /{id}`, `PUT /{id}`, and `DELETE /{id}`  (where `GET`, `PUT`, and `DELETE` are the methods). These examples allow for an `id` to be passed in on the path and that `id` will be used to help fulfill the request.
    
3. The query string.

    - The most common usages for the query string are for filtering, search, and pagination.
    
    - The order of the query string parameters input should not matter.

3. The headers.

    - Headers contain information about the request and information about how the response should be formulated.
     
    - Three common request headers:
    
        1. The `Content-Type` header could be set to `application/json` signifying to the API receiving the request that the body of the request is a JSON formatted string.
        
            *[IETF rfc7231 Content-Type](https://tools.ietf.org/html/rfc7231#section-3.1.1.5)*
        
        2. The `Accepts` header could be set to `application/json, application/xml;q=0.9`, telling the REST API that it will accept a JSON or XML response with a preference for JSON. 
        
            *[IETF rfc7231 Accept](https://tools.ietf.org/html/rfc7231#section-5.3.2)*
            
            *[MDN Accept](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept)*
    
        3. The `Authorization` header is used to pass credentials or OAuth tokens to the endpoint.
        
            *[IETF rfc7235 Authorization](https://tools.ietf.org/html/rfc7235#section-4.2)*
    
4. The body.

    This is the payload for the request. The recommended content type is generally `application/json` but can be of any type.

## API Response Outputs

After making an API request, the response returns three pieces of information:

1. The response status code.

    This code should always be the first thing that an application looks at after receiving and API response and it will indicate the success or failure to fulfill the API request. The [HTTP Methods](#http-methods) section below shows some common response codes for each method, but for a full list of common response codes see [Wikipedia: List of HTTP Status Codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

2. The headers. These headers contain the metadata about the response, including the content type being returned.

3. The body.

    - Response bodies should return the requested resource or resources.
    
    - The response may return some additional computed attributes that are associated with the resource that may not have been included in the request that stored the resource. For example:
     
        - An auto incrementing unique identifier.
         
        - A "last updated date" property.
        
        - [HATEOAS](https://en.wikipedia.org/wiki/HATEOAS) links.
    
    - Avoid presentation layer content. For example, don't send back an HTML page when returning a JSON document would suffice. This lets many different presentation layers use the same set of data.

## HTTP Methods

The HTTP methods specify the action to take on a resource. 

The most common methods include `GET`, `POST`, `PUT` and `DELETE`. DO NOT confuse CRUD (create, read, update, delete) with the `GET`, `POST`, `PUT` and `DELETE` methods. There are important differences.

**Methods Summary**

| Method | Description | Idempotent | Request Body OK |
| ------ | ----------- | ---------- | ------------ |
| GET | Request data about a resource or resources. | Yes | No |
| POST | Create a new resource. | No | Yes |
| PUT | Put a resource to the specified state. | Yes | Yes |
| DELETE | Remove a resource. | Yes | Yes |

*[Wikipedia: Relationship Between URI and HTTP Methods](https://en.wikipedia.org/wiki/Representational_state_transfer#Relationship_between_URI_and_HTTP_methods)*

*[IETF rfc7231 Request Methods](https://tools.ietf.org/html/rfc7231#section-4)*

### GET

- Used to retrieve a single resource or a collection of resources.

- Calling a REST endpoint with `GET` has no side-effects; you can't use `GET` to create, update, or delete a resource.

- The `GET` method can use the path, query parameters, and headers as input but should avoid using the request body.

- For getting a single resource it is common to put the resource identifier in the path. [Examples](#examples-background) of this include `GET /{id}`, `PUT /{id}`, and `DELETE /{id}`.

**Common Response Codes**

- `200` - Resource or resources successfully retrieved.

    *Get a collection with three items*

    ```
    GET /
  
    [
        {
            "id": 1,
            "email": "bob@fake.net"
        },
        {
            "id": 2,
            "email": "amanda@fake.net"
        },
        {
            "id": 3,
            "email": "chris@fake.net"
        }
    ]
    ```
  
    *Get a collection with zero items*

    ```
    GET /
  
    []
    ```
  
    *Get a resource*
    
    Notice how the resource identifier is included in the endpoint's path.
    
    ```
    GET /1
  
    bob@fake.net
    ```

- `400` - There is something wrong with the request. The response body would ideally include a description of what was wrong with the request.

    *[IETF rfc7231 Client Error 4xx](https://tools.ietf.org/html/rfc7231#section-6.5)*
    
    *[IETF rfc7231 400 Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)*

- `401` - The request requires authentication and the request did not have authentication information.

    *[IETF rfc7235 401 Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)*

- `403` - The request provided authentication information but the authenticated user is not authorized to get the results.

    *[IETF rfc7231 403 Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)*

- `404` - This status code commonly indicates one of two things which may be distinguished using the response body. Following the [examples](#examples-background) these two common uses include:

    1. The requested endpoint does not exist.
    
        A request to `GET /2/cats` would return a `404` because there is no endpoint in the API for the specified path.
        
        The response body could say `Endpoint does not exist.`
    
    2. The requested resource does not exist.
    
        A request to `GET /5` would return a `404` because the resource does not exist.
        
        The response body could say `Resource not found.`

    If the endpoint returns a collection then a `404` should not be used when the collection is empty. Instead use a `200` and send back an empty collection.
    
    *[IETF rfc7231 404 Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)*

### POST

- Each `POST` request should generate a new resource.

- Calling the same `POST` API endpoint with the same input parameters and body should either create a new resource or return an error.

- The `POST` is not used to update existing resources.

**Example**

If we `POST` twice to the endpoint `POST /` with the body `sam@fake.net`, then our database would look like this below. Notice that `sam@fake.net` exists twice. This is how a `POST` should work.

| id  | email |
| --- | ----- |
| 1 | bob@fake.net |
| 2 | amanda@fake.net |
| 3 | chris@fake.net |
| 4 | sam@fake.net |
| 5 | sam@fake.net |

Common response codes for a `POST` request include:

- `201` - The resource was created. If a response body is provided then it should represent the resource created and look very similar (ideally identical) to the resource returned by `GET /{id}`.

    Additionally, a `Location` header should be set, specifying the endpoint for where the created resource can be retrieved. For example, if the created resource had `5` as an identifier then the location header would look like this `Location: http://email-registry.com/5` or like this `Location: /5` (location relative to POST request location).
    
    *[IETF rfc7231 POST](https://tools.ietf.org/html/rfc7231#section-4.3.3)*

- `400` - There is something wrong with the request. The response body would ideally include a description of what was wrong with the request.

    *[IETF rfc7231 Client Error 4xx](https://tools.ietf.org/html/rfc7231#section-6.5)*
    
    *[IETF rfc7231 400 Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)*

- `401` - The request requires authentication and the request did not have authentication information.

    *[IETF rfc7235 401 Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)*

- `403` - The request provided authentication information but the authenticated user is not authorized to perform the action.

    *[IETF rfc7231 403 Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)*

- `404` - The requested endpoint was not found.
    
    *[IETF rfc7231 404 Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)*

### PUT

- A `PUT` method signifies that the state of a resource should be set to the provided input.

- This `PUT` method is idempotent. Calling the same endpoint multiple times with the same input will not change the resource to be anything different than the first request. It also will have no side-effects for the system. 

- `PUT` should not be used for partial updates of a resource. If you must support this use the `PATCH` method that has not yet been fully standardized but is intended for partial updates.

- `PUT` endpoints will commonly include the identifier in the endpoint's path.

**Example**

Calling the endpoint `PUT /1` multiple times with the body `sam@fake.net` will always result in the email address being set to `sam@fake.net` for ID `1`.

| id  | email |
| --- | ----- |
| 1 | sam@fake.net |
| 2 | amanda@fake.net |
| 3 | chris@fake.net |

Common response codes for a `PUT` request include:

- `200` - The resource already existed and was set to the specified value. If a response body is provided then it should represent the resource created and look very similar to the resource returned by `GET /{id}`.

    *[IETF rfc7231 PUT](https://tools.ietf.org/html/rfc7231#section-4.3.4)*

- `201` - The resource was created. If a response body is provided then it should represent the resource created and look very similar to the resource returned by `GET /{id}`.

    *[IETF rfc7231 PUT](https://tools.ietf.org/html/rfc7231#section-4.3.4)*

- `400` - There is something wrong with the request. The response body would ideally include a description of what was wrong with the request.

    *[IETF rfc7231 Client Error 4xx](https://tools.ietf.org/html/rfc7231#section-6.5)*
    
    *[IETF rfc7231 400 Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)*

- `401` - The request requires authentication and the request did not have authentication information.

    *[IETF rfc7235 401 Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)*

- `403` - The request provided authentication information but the authenticated user is not authorized to perform the action.

    *[IETF rfc7231 403 Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)*

- `404` - The requested endpoint was not found or the requested resource did not exist. See the documentation for the [GET](#get) `404` response code for more details about the difference between these two possible causes for the `404`.
    
    *[IETF rfc7231 404 Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)*

### DELETE

- This `DELETE` method is idempotent and calling the same endpoint multiple times will result in the resource being deleted (or remaining deleted) without side-effects for the system.

- `DELETE` endpoints will commonly include the identifier in the endpoint's path.

- `DELETE` endpoints should not return the content that would be returned by a `GET /{id}` request because if the `DELETE` is called more than once then the content for the resource will not exist on the second request.

Common response codes for a `DELETE` request include:

- `204` - The resource was deleted (i.e. now does not exist) and no response body is being sent.

    *[IETF rfc7231 DELETE](https://tools.ietf.org/html/rfc7231#section-4.3.5)*

- `400` - There is something wrong with the request. The response body would ideally include a description of what was wrong with the request.

    *[IETF rfc7231 Client Error 4xx](https://tools.ietf.org/html/rfc7231#section-6.5)*
    
    *[IETF rfc7231 400 Bad Request](https://tools.ietf.org/html/rfc7231#section-6.5.1)*

- `401` - The request requires authentication and the request did not have authentication information.

    *[IETF rfc7235 401 Unauthorized](https://tools.ietf.org/html/rfc7235#section-3.1)*

- `403` - The request provided authentication information but the authenticated user is not authorized to get the results.

    *[IETF rfc7231 403 Forbidden](https://tools.ietf.org/html/rfc7231#section-6.5.3)*

- `404` - The requested endpoint was not found.

    Don't use this to indicate that the resource was not found, use `204` instead to indicate that it does not exist (i.e. is deleted or gone). One of the big advantages of idempotent methods, like `DELETE`, is that they can be retried in case of network fail, process fail, etc. In that case many clients (web browsers included) will auto retry the request and if the first request succeeded but the response never made it to the client then the retry request should still receive a success response of `204`.

    *[IETF rfc7231 404 Not Found](https://tools.ietf.org/html/rfc7231#section-6.5.4)*

# Versioning

Once an API is published it must not break the contract by changing endpoints or altering existing fields. It is OK to add additional fields and endpoints.

It is important to version your API so that if the need arises to break your API contract you can create a new version that users can begin to point their applications to. You should not disable previous API versions until no one is using them, although it is important to mark them as deprecated.

Versioning your API is commonly done by prefixing the version information to the path, following the domain.

Examples:

- `http://api.email-registry.com/v1/`

- `http://api.email-registry.com/v2/`

*[Microsoft REST API Guidelines: Versioning](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#12-versioning)*

*[Wikipedia: Web API](https://en.wikipedia.org/wiki/Web_API#Endpoints)*

*[RESTful API Designing Guidelines](https://hackernoon.com/restful-api-designing-guidelines-the-best-practices-60e1d954e7c9)*

*[REST API Versioning Strategies](https://www.xmatters.com/integrations-blog/blog-four-rest-api-versioning-strategies/)*
