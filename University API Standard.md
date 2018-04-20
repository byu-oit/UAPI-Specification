# University API Standard

Version 1.0

The University API Standard is licensed under [The Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).

## Introduction

The University API Standard defines the technical structure recommended when designing and implementing an API in the University API URL namespace (api.byu.edu/byuapi), or in the namespace of a specific domain (e.g., api.byu.edu/domains/erp). The University API Standard specifically addresses URL design, HTTP method selection and use, and structural representations of the recommended mime-type (application/json), including the use of a custom HATEOAS link design pattern to convey available operations and state transitions. These standards will provide consistency for both on-campus and external consumers of APIs.

## Terms

|Term|Definition
|-----|-----
|Contract|A published definition of a resource and its associated representation and URL design (constituting an API) that consumers rely upon.
|HATEOAS|[Hypermedia As The Engine Of Application State](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm#sec_5_1_5)
|HTTP|Hypertext Transfer Protocol](https://tools.ietf.org/html/rfc2616)|
|Property|Key/value pairs defined in a representation.
|Representation|The structure of a domain-model object in the context of a specified mime-type. A representation is what is returned in response to a request or what is submitted in the body of a request.
|Resource|A domain-model object that has a structural representation and set of allowable HTTP methods.
|REST|[Representational State Transfer](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
|URL|[Uniform Resource Locator](https://tools.ietf.org/html/rfc1738)

## URL Design

To adhere to this standard, URL design should follow the patterns:

```
https://host/namespace/resource/identifier
```

```
https://host/namespace/top-level-resource/top-level-resource-identifier/sub-resource/sub-resource-identifier
```

The simplest pattern listed first conveys the convention of defining a namespace, identifying a resource, and specifying the type and form of the identifier of the resource. The following documentation will detail the second listed pattern that defines the BYU Standard implementation of:

-   namespace
-   resource
-   top-level resource
-   resource identifier
-   sub-resource

### Namespace

BYU APIs are segregated into two distinct categories: the University API and Domain APIs. The University API represents the collaborative product of common Domain APIs addressing common business use cases and establishing a common naming convention of resources and properties that represent a University API interchange language. Following the [Domain Driven Design](http://dddcommunity.org/) philosophy, Domain APIs are produced and managed by identified domains within the University. The differences between the University API and Domain APIs are:

|University API|Domain/Legacy API
|-------------|------------------
|Recommended option for consuming an API|Should only be used in specific situations allowed by the domain team maintaining the API
|Data structure kept consistent across current major version|Data structure may change according to needs of domain team
|New updates remain compatible with existing code across current major version|New updates may lead to incompatibility with subscribed applications
|Swagger meets University API standard|Swagger may not meet the University API standard**
|Contains data and procedures for which there is a strong or common business use case|Contains data and procedures which are generally common but should be accessed through the University API, or which may be relevant to only a small group of users
|Ideal for external and cross-departmental use|Ideal for use by the domain team which published it
|Enables users to gain authorization for particular fieldsets|Requires authorization for all elements of the API

**The comment above which states that Domain/Legacy APIs' Swagger documents may not meet the University API standard is intended as a warning to consumers, not as a free pass to publishers. All APIs published to the API Store are expected to comply with the guidelines described in the University API standard.

Particularly, both the University API and Domain APIs have distinct namespaces. The namespace designated for the University API is:

```
https://api.byu.edu/byuapi
```

The namespace designated for Domain APIs is:

```
https://api.byu.edu/domains/{domain_name}
```

The *domain_name* portion of the Domain API namespace is determined by the domain team. For example, if a domain team had chosen the domain_name "identity," its namespace would be:

```
https://api.byu.edu/domains/identity
```

### Resource

The resource portion of the URL design represents a category of data or domain model entity managed by the API. For example, the resource "identities" contains all data related to a University identity; the resource "identities" represents a collection of identities and any data related to that collection of identities.

Examples of URLs pointing to resources are:

University API:

```
https://api.byu.edu/byuapi/identities
```

Domain API:

```
https://api.byu.edu/domains/identity/identities
```

If the resource represents a collection of data (meaning that, if you invoke an HTTP GET method against the resource, it returns a collection, set, or list of that defined resource), then it must be written in the plural form, i.e. *identities* rather than *identity*.

### Top-level Resources

Resources are "top-level" resources if they are the first resource under the namespace in which they are found.

Examples of URLs pointing to a top-level resource are:

University API:

```
https://api.byu.edu/byuapi/identities
```

The resource "identities" is the first resource after the University API namespace (byuapi).

Domain API:

```
https://api.byu.edu/domains/identity/identities
```

The resource "identities" is the first resource after the "identity" domain namespace.

### Resource Identifier

If a resource represents a collection of data, an item in that resource collection should also be addressable in the API URL.

Examples of URLs pointing to the addressable form of an item in a collection of resources are:

University API:

```
https://api.byu.edu/byuapi/identities/{identifier_key}
```

```
https://api.byu.edu/byuapi/identities/123456789
```

Domain API:

```
https://api.byu.edu/byu.edu/domains/identity/identities/{identifier_key}
```

```
https://api.byu.edu/byu.edu/domains/identity/identities/123456789
```

The identifier value "*123456789*" is the key of an item in the collection resource "*identities*". If you were to invoke an HTTP GET method against the example URL "*https://api.byu.edu/identities/123456789*", the result would be the structural representation of a single "*identity*" item that **at least contains the identifier key** "*123456789*" as a property.

Note: If a resource is not a collection, the plural form of the resource name is still used to represent the resource in the URL, but there is no need to include an identifier in the URL or in the resource representation that is returned. Simply calling the resource URL will return all relevant information because there is only one resource.

#### Resource Composite Identifier

Some resources must be identified using a composite identifier. A composite identifier uniquely identifies a resource by multiple pieces of information instead of just one.

Composite identifiers should conform to the pattern:

```
namespace/resource/{key}[,{additional_keys}]
```

Examples of URLs pointing to composite resources are:

University API:

```
https://api.byu.edu/byuapi/classes/Fall+2016,MATH+110,001
```

Domain API:

```
https://api.byu.edu/byu.edu/domains/class_schedule/classes/20165,06387,001,001
```

Note:In the URL, use '+' to designate white space and ',' as a composite key delimiter. The symbol "+" (plus sign) is a standard URL special character used in place of a single white space. The symbol "," (comma) is the University API Standard character indicating separation between two parts of a composite key. More information on path definitions can be found [here](/docs/debug-api/my-api-isnt-accepting-special-characters).

When defining a path in a [Swagger document](/docs/design-api/good-api-design), good design practice is to fully qualify the path that also includes composite keys:

```

{
    "swagger": "2.0",
    "info": {
        "version": "v1",
        "title": "Classes",
        "license": {
            "name": "Apache-2.0"
        }
    },
    "host": "api.byu.edu",
    "basePath": "/domains/class_schedule/classes",
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "paths": {
      "/{year_term},{curriculum_id},{title_code},{section_number}": {
            "get": {
                "summary": "Retrieve specific class",
                "operationId": "getClass",
                "parameters": [
                    {
                        "name": "year_term",
                        "in": "path",
                        "required": true,
                        "description": "Year term",
                        "type": "string"
                    },
                    {
                        "name": "curriculum_id",
                        "in": "path",
                        "required": true,
                        "description": "Curriculum identifier",
                        "type": "string"
                    },
                    {
                        "name": "title_code",
                        "in": "path",
                        "required": true,
                        "description": "Title code",
                        "type": "string"
                    },
                    {
                        "name": "section_number",
                        "in": "path",
                        "required": true,
                        "description": "Section number",
                        "type": "string"
                    }
                ]
            }
...
        }
    }
}
```

### Sub-resources

Resources are "sub-resources" if they fall under a top-level resource in the URL path structure.

Examples of URLs pointing to sub-resources are:

University API:

```
https://api.byu.edu/byuapi/identities/123456789/addresses
```

```
https://api.byu.edu/byuapi/identities/123456789/classes
```

The resources "addresses" and "classes" fall under the top-level resource "identities." The sub-resource "classes," being a collection, will require a sub-resource identifier. The sub-resource "addresses", assuming it is not a collection of resources, will not have a sub-resource identifier.

```
https://api.byu.edu/byuapi/identities/123456789/classes/Fall+2016,MATH+110,001
```

Domain API:

```
https://api.byu.edu/domains/identity/identities/123456789/addresses
```

```
https://api.byu.edu/domains/identity/identities/123456789/classes
```

The resources "addresses" and "classes" fall under the top-level resource "identities." The sub-resource "classes," being a collection, will require a sub-level-resource identifier. The sub-resource "addresses", assuming it is not a collection of resources, will not have a sub-resource identifier.

```
https://api.byu.edu/domains/identity/identities/123456789/classes/20165,06387,001,001
```

Note: Simplify the options on the URL segments and do not allow redundant URL paths. Although there may be multiple ways to logically represent a specific resource in your URL design, you should only allow for one option in your code. This simplifies things for the consumer and provides a better experience overall. For example, you might think of multiple ways in the URL design to access a resource "classes":

-   teaching area and catalog number
    
    ```
    https://api.byu.edu/domains/curriculum/courses/MATH+110
    ```
    
-   curriculum\_id and title\_code
    
    ```
    https://api.byu.edu/domains/curriculum/courses/06387,001
    ```
    

When faced with different options of URL resource identifiers to choose from, you must choose to implement only one of the options on the URL path. If the first option of using the teaching area and catalog number were employed, the curriculum\_id and title\_code values could be implemented as query parameters to filter on the collection of resources. For example:

```
https://api.byu.edu/domains/curriculum/courses?curriculum_code=06387&title_code=001
```

Note: The teaching area and catalog number are commonly represented as text separated by a space. The example above represents those combinations as a single identifier (not a composite identifier) but separated by a space in that identifier value. Also, take note that the University API example uses the teaching area and catalog number as its identifiers and the domain uses the curriculum\_id and title\_code values as its identifiers. If you tried to call the University API with the curriculum\_id and title\_code as the identifiers, you would get an error. The domain API would likewise return an error if using the wrong identifiers.

## Resource Representation


The aggregated properties of a resource combine to define the *representation* of a resource. The resource representation is provided as the response to HTTP GET requests or as input to HTTP PUT or POST requests. The University API Standard recommends the default mime-type for the structure of a representation to be "application/json". **All APIs in the University API namespace (https://api.byu.edu/byuapi) will implement "application/json" as the only supported Content-Type mime-type.** This section of the University API Standard defines the patterns in designing representations for API resources:

The following are required properties found at the root of the JSON object representation for a non-collection resource

-   links
-   metadata

Example:

```
{
  "links": {},
  "metadata": {},
  "property_1": {},
  "property_2": {}
}

```

The following are required properties found at the root of the JSON object representation for a collection of resources

-   links
-   metadata
-   values

```
{
  "links": {},
  "metadata": {},
  "values": []
}

```

It is important to note that the API contract defined by the resource representation is associated to the URL resource name and not by any URL segments added to the resource name, such as query parameters. An example of a consistent resource representation associated with a URL resource would be a call to a specific identity resource:

```
https://api.byu.edu/byuapi/identities/123456789
```

The resource representation returned would be:

```
{
  "links":{},
  "metadata": {},
  "property_1": {}
}
```

If a call to the resource collection was made with a filtering query parameter,

```
https://api.byu.edu/byuapi/identities?id=123456789
```

the resource representation returned would be for the collection (https://api.byu.edu/byuapi/identities). Notice also, that each item resource representation in the collection is the same as a specific identity resource request (https://api.byu.edu/byuapi/identities/123456789).

```
{
  "links":{},
  "metadata": {},
  "values": [
    {
      "links":{},
      "metadata": {},
      "property_1": {}
    }
  ]
}
```

### Links

The `links` property is an object that contains named properties that are patterned after the [HATEOAS](https://en.wikipedia.org/wiki/HATEOAS) approach. Our definition and use of a HATEOAS link includes the following properties:

|Property|Description
|-----|-----
|rel|The rel contains the same name as that of the HATEOAS link object name. "self" is a special value for rel that is used for the HATEOAS link synonymous to the URL that got you the current result. The value of the rel should be in the form of <resource-name>__<action>.
|href|The href contains the URL used to perform the HATEOAS link action (this may also be a templated URL according to [RFC 6570](https://tools.ietf.org/html/rfc6570)).
|method|The method contains the HTTP method used when invoking the URL in the href property.

An example links object for the identities resource collection would look like:

```
  "links": {
    "identities__info": {
      "rel": "self",
      "href": "https://api.byu.edu/byuapi/identities?page_start=51,page_size=50",
      "method": "GET"
    },
    "identities__prev": {
      "rel": "identities__prev",
      "href": "https://api.byu.edu/byuapi/identities?page_start=1,page_size=50",
      "method": "GET"
    },
    "identities__next": {
      "rel": "identities__next",
      "href": "https://api.byu.edu/byuapi/identities?page_start=101,page_size=50",
      "method": "GET"
    },
    "identities__create": {
      "rel": "identities__create",
      "href": "https://api.byu.edu/byuapi/identities",
      "method": "POST"
    }
  },

```

A `links` object should be included in the representation result that at least contains a "self" HATEOAS link.

When a domain API *(APIs that are under the `/domains/` context path)* is implemented in a third-party product that has limitations in customizing the API, here are three separate suggested approaches in which domain APIs may facilitate University API HATEOAS compliance:

-   Implement a University API Standard compliant API which cleanly can translate HATEOAS links from the domain language to the UAPI interchange language (representations and HATEOAS links)
-   Expose the domain business logic in some convention (custom HATEOAS links, other data structure) which the UAPI implementation can use to translate to the UAPI interchange language (representations and HATEOAS links)
-   Provide data that the UAPI implementation uses with embedded logic in the UAPI implementation that represents the domain business logic which is translated to the UAPI interchange language (representations and HATEOAS links)

### Metadata

A *metadata* object is always included in the resource representation except when a specific top-level resource is requested.

|Property|Required|Description
|-----|-----|-----
|validation_response|yes|This is an object that contains two required properties: *code* and *message*.
|validation_information|no|This is an array of strings that provide information about errors correlated to the validation_response.code and HTTP response code.
|cache|required if the result is a cached value|This is an object that contains one required property: *date_time*. The date_time value is when the data was updated in the cache.
|collection_size|required if the result is a resource representation for a collection|The number of items of the resource which exist in the collection. This is not how many were returned in this "page" of the collection.
|default\_page\_size|required if the result is a resource representation for a collection and the API implements support for paging|Unless overridden in query parameters, this endpoint would default to the following "page" size.
|max\_page\_size|required if the result is a resource representation for a collection and the API implements support for paging|The largest "page" size that the implementation of the API will support.
|page_size|required if the result is a resource representation for a collection and the API implements support for paging|The number of items which were returned in this "page" of the collection.
|page_start|required if the result is a resource representation for a collection and the API implements support for paging|The item of the collection on which the returned "page" of the collection starts
|page_end|required if the result is a resource representation for a collection and the API implements support for paging|The item of the collection on which the returned "page" of the collection ends

##### Examples

Success on a top-level resource collection:

```

"metadata": {
  "validation_response": {
    "code": 200,
    "message": "Success"
  },
  "collection_size": 150,
  "default_page_size": 50,
  "max_page_size": 1000,
  "page_size": 50,
  "page_start": 51,
  "page_end": 100
 }

```

Success on a specific top-level resource: *Note: The basic field\_set is included in this example to show that the metadata object is not included in the top-level specific resource representation, but should instead be accessed at the basic field\_set or other returned field_sets of the returned resource representation.*

```

{
  "basic": {
    "links": {
      "persons_info": {
        "rel": "self",
        "href": "https://api.byu.edu/byuapi/persons/123456789",
        "method": "GET"
      }
    }
    "metadata": {
      "validation_response": {
        "code": 200,
        "message": "Success"
      }
    },
    "byu_id": "123456789"
  }
}

```

Error on a top-level resource collection:

```

"metadata": {
  "validation_response": {
    "code": 403,
    "message": "Not Authorized"
  },
  "validation_information": [
    "Not authorized"
  ]
 }

```

Error on a specific top-level resource: *Note: The basic field\_set is included in this example to show that the metadata object is not included in the top-level specific resource representation, but should instead be accessed at the basic field\_set or other returned field_sets of the returned resource representation.*

```

{
  "basic": {
    "metadata": {
      "validation_response": {
        "code": 403,
        "message": "Not Authorized"
      },
      "validation_information": [
        "Not authorized to see basic field_set"
      ]
    }
  }
}

```

Success on a sub-resource collection: *Note: This sub-resource does not implement paging, so the paging properties are omitted.*

```

"metadata": {
  "validation_response": {
    "code": 200,
    "message": "Success"
  },
  "collection_size": 150
}

```

Success on a specific sub-resource: *Note: The specific sub-resource is a cached response from the service, so the date_time of when the data was current is provided and the consumer of the service can determine if that is sufficient for its business purposes.*

```

"metadata": {
  "validation_response": {
    "code": 200,
    "message": "Success"
  },
  "cache": {
    "date_time": "2017-01-01T11:11:11.011Z"
  }
}

```

Error on a sub-resource collection:

```

"metadata": {
  "validation_response": {
    "code": 403,
    "message": "Not Authorized"
  },
  "validation_information": [
    "Not authorized"
  ]
 }

```

Error on a specific sub-resource:

```

"metadata": {
  "validation_response": {
    "code": 403,
    "message": "Not Authorized"
  },
  "validation_information": [
    "Not authorized"
  ]
 }

```

### Values

The `values` array is used if the resource representation is a collection. The array shall contain items that are the resource representation of the specific resource type in that collection. For example, the items in the values array for a top-level resource would be the specific top-level resource representation. Or, the items of the values array for a sub-resource collection would be the specific sub-resource representation. Here are examples of a top-level and sub-resource collection using the values array:

##### Examples

Top-level resource collection: *Note: The specific top-level resource representation always has field_sets as properties.*

```

"values": [
  {
    "basic": {
      "links": {
        "persons_info": {
          "rel": "self",
          "href": "https://api.byu.edu/byuapi/persons/123456789",
          "method": "GET"
        }
      }
      "metadata": {
        "validation_response": {
          "code": 200,
          "message": "Success"
        }
      },
      "byu_id": "123456789"
    }
  }
]

```

Sub-resource collection: *Note: The sub-resource representation does not contain field_set properties but directly contains links, metadata, and properties of the resource type.*

```

"values": [
  {
    "links": {
      "persons_info": {
        "rel": "self",
        "href": "https://api.byu.edu/byuapi/persons/123456789",
        "method": "GET"
      }
    }
    "metadata": {
      "validation_response": {
        "code": 200,
        "message": "Success"
      }
    },
    "byu_id": "123456789"
  }
]

```

### Properties

Properties in a resource representation are actually represented as complex objects with detail properties that are outlined below:

|Property|Required|Description
|-----|-----|-----
value|Required if the api_type is not a value communicating an error.|Contains the data value to be processed.
|api_type|Yes|Describes how this value may be used in this API. For a list of the possible values for api_type and the rules associated with each value, click [here](#UniversityAPIStandard-api_type_list).
|key|Required if the property is the key identifier or one of the composite identifiers of the resource.|Designates that the property is one of the key elements for this resource. This has several implications. First, key fields are required fields - they are not allowed to be blank or null. Second, it is used in the URL segment of the URL to access a specific item in the resource collection. Some resources may not allow a key value to be modified, but there are the exceptions. Refer to the documentation for the API. Some resources may assign the key value when the item is added. Once again, refer to the documentation for the API.
|description|No|Explains the data value in human-friendly terms.
|display_label|No|Provides a string to use when creating a label for this property in the user interface if the name of the property is not appropriate (or is too cryptic) to use as a user interface label.
|domain|Required if the value property is part of a set of allowable codes.|Contains the URL that can be used to retrieve the set of allowable codes. The result of invoking the URL could be used to populate the UI's way of choosing a value from the allowable set.```"domain": "https://api.byu.edu/byuapi/meta/year_terms"```
|long_description|No|Explains the data value in human-friendly terms; contains more information than the (short) description.
|related_resource|Required if the api_type property is "related".|If the api_type is "related", this property will contain the resource-name that "owns" this property. The resource-name can be used to find a HATEOAS link that can access this property.
```

                        "links": {
                            "applicants__info": {
                                "rel": "self",
                                "href": "https://api.byu.edu/cesapi/applicants/v1/764634873",
                                "method": "GET"
                            },
                            "persons__info": {
                                "rel": "persons__info",
                                "href": "https://api.byu.edu/byuapi/persons/v1/764634873",
                                "method": "GET"
                            }
                        },
                        "metadata": {
                            "validation_response": {
                                "code": 200,
                                "message": "Successful"
                            }
                        },
                        "applicant_id": {
                            "value": "764634873",
                            "api_type": "system",
                            "key": true,
                            "display_label": "Applicant ID"
                        },
                        "person_id": {
                            "value": "585241162",
                            "api_type": "related",
                            "related_resource": "persons",
                            "display_label": "AIM Person ID"
                        }

```

##### Example

```
"byu_id": {
  "api_type": "system",
  "display_label": "BYU ID",
  "key": true,
  "value": "764634873"
}
```

#### api_type

Rarely will a resource property use all of the available detail properties, but `api_type` will always be included. The possible values of `api_type` are:

|api_type|Connotes that the value may be modifiable by the consumer of the API|Description
|-----|-----|-----
read-only|No|Designates that the value may potentially be modified, but that there are either business rules or authorization considerations that do not allow the value to be changed at this time by the requesting entity.
|modifiable|Yes|Designates that the value may be modified.
|system|No|Signifies that the value was assigned by the system. If it can be modified, it will be by the system and not through API input. A date-time field that tracks the last time the resource was updated is a good example of a system data value.
|derived|No|Designates that the value was derived by a "calculation" on other properties. An example might be GPA. An API will not support the modification of derived data. As an example, to modify a GPA, you would have to modify grades or credit hours on individual classes.
|unauthorized|No|Designates that this property is not authorized to be retrieved by the requesting entity.
|related|No|Signifies that the value was included in this API to make the API more useful, but it doesn't really belong to this API. The business logic to modify the property exists in the "related_resource".
```

                        "links": {
                            "applicants__info": {
                                "rel": "self",
                                "href": "https://api.byu.edu/cesapi/applicants/v1/764634873",
                                "method": "GET"
                            },
                            "persons__info": {
                                "rel": "persons__info",
                                "href": "https://api.byu.edu/byuapi/persons/v1/764634873",
                                "method": "GET"
                            }
                        },
                        "metadata": {
                            "validation_response": {
                                "code": 200,
                                "message": "Successful"
                            }
                        },
                        "applicant_id": {
                            "value": "764634873",
                            "api_type": "system",
                            "key": true,
                            "display_label": "Applicant ID"
                        },
                        "person_id": {
                            "value": "585241162",
                            "api_type": "related",
                            "related_resource": "persons",
                            "display_label": "AIM Person ID"
                        }

```

## URL Query Parameters

Query strings are used to filter through a collection, returning all resources whose values match what has been specified. For example, "[https://host/cars?color=red&make=Ford](https://host/cars?color=red&make=Ford)" returns a JSON array that only contains red Fords. Similarly, "[https://host/students?major=CS](https://host/students?major=CS)" returns a JSON array that contains only students whose major is Computer Science (CS). 

This is the standard structure of a query string: 

```
https://host/namespace/resource?query-parameter=value
```

```
https://host/namespace/top-level-resource/top-level-resource-identifier/sub-resource?query-parameter=value
```

Note: Do not attempt to GET a resource collection using the structure *namespace/resource/query-parameter/value* (e.g., *.../students/major/CS*). Such a call does not make sense.

collection parameters

-   page_size
-   page_start
-   page_end

filter parameters

-   identifiers
-   properties

## HTTP Methods

The following is a list of recommended HTTP methods to support in an API and the philosophy behind each HTTP method.
  
|HTTP Method|Description|Notes
|-----|-----|-----
|OPTIONS|Returns the set of http_methods allowed for the URL that addresses a collection|According to HTTP protocol the list of options is returned in an HTTP header with a key name of "Allow". For example, if a URL allows the GET, POST, and OPTIONS methods, the HTTP header would contain "Allow: GET,POST,OPTIONS". Similarly, the University API returns a JSON array in the body of the response that lists all supported methods. The JSON array looks like this:```{"supported_methods": ["GET","POST","OPTIONS"]}```
|GET|Returns a resource or collection of resources.|Sometimes, a combination of query string filters or URL segments may return only one (or even zero) items. That's okay. Even the empty set is a valid set. In the University API, we have elected to always return a JSON array structure, even when there is only one or zero items returned. This allows the developer to code for a predictable JSON data structure. We don't change the contract of the return body based on the number of items selected.
|POST|Creates a new resource. If the resource is part of collection, the request creates a new item in the collection.|In most instances, the body will contain a single item to be added, but the request body may contain a JSON array structure of resource(s) to be created (added). The return body is the item that was added or a JSON array structure that contains information about each resource for which an add attempt was made.
|PUT|Modifies the resource or a set of resources in a collection.|While there may be cases where we perform bulk updates, we have tried to avoid them in the University API. Generally, the URL defines a single item instead of a collection, and that single item is modified to "look like" the resource in the request body. The modified resource is generally then returned in the return body. *Sub-resources are added and removed by put, not by post or delete*.
|DELETE|Deletes the resource or collection of resources.|While there may be cases for bulk deletes, we have tried to avoid them in the University API. Since the URL completely defines the resource to be deleted, there is generally no request body or return body for this method.

## HTTP Headers
|HTTP Header Name|Type|Description
|-----|-----|-----
|Accept|Request
|Content-Type|Response
|Authorization|Request

## HTTP Status Codes
The following HTTP status codes and the suggested descriptions are the minimal set of status codes to support in an API.

|HTTP Status Code|Description
|-----|-----
|200|Request was successful
|400|Request not understood (syntax error)
|401|Request not authenticated
|403|Request not authorized
|500|Server error
