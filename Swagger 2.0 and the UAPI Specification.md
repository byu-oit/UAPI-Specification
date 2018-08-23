# Best Practices in writing Swagger documentation for UAPI compliant services
​
This document addresses how to create an Open API 2.0 (aka Swagger 2.0) document that conforms to the [University API Standard](). This document's outline will follow the order of the properties defined in the [Open API 2.0 specification](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) and for specific sections qualify how to implement the section in following the University API Standard.
​
## swagger
No qualification necessary
​
## info
Here is an example of an `info` object:
​
```JSON
  "info": {
  "title": "Persons",
  "description": "Persons Resource of the University API",
  "version": "v3",
  "contact": {
    "name": "OIT Service Desk Support",
    "url": "https://it.byu.edu",
    "email": "it@byu.edu"
  },
  "license": {
    "name": "Apache 2.0",
    "url": "http://www.apache.org/licenses/LICENSE-2.0.html"
  }
}
```
​
### title
The name of the API that needs to be unique in the BYU API Publisher (https://api.byu.edu/publisher) and BYU API Store (https://api.byu.edu/store)
​
### description
No qualification necessary
​
### termsOfService
Currently not necessary or used to conform to the University API Standard.
​
### contact
The contact object should look exactly like the following:
​
```JSON
"contact": {
  "name": "OIT Service Desk Support",
  "url": "https://it.byu.edu",
  "email": "it@byu.edu"
}
```
​
### license
The license for all services should be the Apache 2.0 license.
​
```JSON
"license": {
  "name": "Apache 2.0",
  "url": "http://www.apache.org/licenses/LICENSE-2.0.html"
}
```
​
```YAML
license:
  name: Apache 2.0
  url: http://www.apache.org/licenses/LICENSE-2.0.html
```
​
### version
The `version` attribute is a "required" attribute and should take the form of `v{number}`. We are **NOT** following the the [semantic versioning notation](https://semver.org/). We are only indicating major version numbers as integers.
​
```JSON
"version": "v1"
```
​
```YAML
version: v1
```
​
## host
The `host` value should always be: `api.byu.edu`.
​
```JSON
"host": "api.byu.edu"
```
​
```YAML
host: api.byu.edu
```
​
## basePath
If the service is a part of the University's "BYU API" services, then the `basePath` should be prefixed with `/byuapi/` followed by the name of the top-level resource of the API. A templated example is: `/byuapi/{top-level-resource}`.
​
```JSON
"basePath": "/byuapi/persons",
```
​
If the service is a domain service, then the `basePath` should be prefixed with `/domains/` followed by the name of the domain and top-level resource of the domain API. A templated example is: `/domains/{domain_name}/{top-level-resource-of-domain}`.
​
```JSON
"basePath": "/domains/identity/persons"
```
​
## schemes
UAPI compliant services currently only support secure HTTP services. The `schemes` array should look exactly like the following:
​
```JSON
"schemes": [
  "https"
],
```
​
## consumes
The `consumes` attribute conveys what [mime-types/media-types](https://www.iana.org/assignments/media-types/media-types.xhtml) the API accepts as valid input when using POST and PUT HTTP methods. UAPI compliant services always will include `application/json` as a supported mime-type. If the API supports paths that implement file payloads, the mime-type of the supported file type must be specified in this list.
​
```JSON
"consumes": [
  "application/json"
],
```
or
​
```JSON
"consumes": [
  "application/json",
  "image/jpeg"
],
```
​
## produces
The `produces` attribute conveys what [mime-types/media-types](https://www.iana.org/assignments/media-types/media-types.xhtml) the API returns as a valid result when using the GET HTTP method. UAPI compliant services always will include `application/json` as a supported mime-type. If the API supports paths that implement file payloads, the mime-type of the supported file type must be specified in this list.
​
```JSON
"produces": [
  "application/json"
],
```
or
​
```JSON
"produces": [
  "application/json",
  "image/jpeg"
],
```
​
## paths
There are no special recommendations for Path Item objects except that when specifying a Response object under an Operation object that contains a `schema` object, the definition should provide an example under the `schema` object's sibling `examples` object. A strong recommendation is to use the `x-mock_json` object to define all your examples and then reference them where needed throughout any portion of the Open API 2.0 document.
​
Here are some examples:
​
```JSON
"responses": {
  "200": {
    "description": "A collection of Persons successfully returned",
    "schema": {
      "$ref": "#/definitions/persons"
    },
    "examples": {
      "application/json": {
        "$ref": "#/x-mock_json/persons"
      }
    }
  },
  "400": {
    "$ref": "#/responses/400"
  },
  "401": {
    "$ref": "#/responses/401"
  },
  "403": {
    "$ref": "#/responses/403"
  },
  "404": {
    "$ref": "#/responses/404"
  },
  "409": {
    "$ref": "#/responses/409"
  },
  "500": {
    "$ref": "#/responses/500"
  },
  "503": {
    "$ref": "#/responses/503"
  },
  "504": {
    "$ref": "#/responses/504"
  },
  "default": {
    "$ref": "#/responses/default"
  }
}
```
​
## definitions
For each schema object, the API should provide an example object. A strong recommendation is to use the `x-mock_json` object to define all your examples and then reference them where needed throughout any portion of the Open API 2.0 document.
​
Example schema object under the `definitions` object:
​
```JSON
"person_post": {
  "description": "Person information to create",
  "type": "object",
  "required": [
    "surname",
    "first_name"
  ],
  "properties": {
    "byu_id": {
      "description": "only included if updating from PRO",
      "type": "string",
      "maxLength": 9
    },
    "person_id": {
      "description": "only included if updating from PRO",
      "type": "string",
      "maxLength": 9
    },
  },
  ...
  "example": {
    "$ref": "#/x-mock_json/person_post"
  }
},
​
```
​
## parameters
Parameters can be defined at the root of the Open API 2.0 document, the Path Item object, and in the Operation object. If the parameter is common across Path Item objects, then the parameter definition should be under the root `parameters` object. If the parameter is common across Operation objects under a single Path Item object, then the parameter definition should be under the Path Item `parameters` object. If the parameter is unique only to the particular Operation object inside a Path Item object, then the parameter definition should be under that Operation object. It's important to note that even though you define the parameter at the root `parameters` object, that definition alone does not automatically make it globally applicable. Always reference the root definition under the Path Item `parameters` object or the Operation `parameters` object where it applies.
​
Examples of standard parameters to put in the root `parameters` object that are for top-level resources are:
​
- field_sets
​
```JSON
{
  "name": "field_sets",
  "in": "query",
  "description": "indicates which field sets are to be returned",
  "required": false,
  "type": "array",
  "default": [
    "basic"
  ],
  "items": {
    "type": "string",
    "enum": [
      "basic",
      "addresses",
      "alias_domains",
      "credentials",
      "email_addresses",
      "email_aliases",
      "employee_summaries",
      "family_phones",
      "vital_records",
      "group_memberships",
      "groups_administered",
      "id_cards",
      "languages",
      "personal_records",
      "phones",
      "relationships",
      "student_summaries"
    ]
  }
}
```
- contexts
​
```JSON
{
  "name": "contexts",
  "in": "query",
  "description": "indicates which contexts are to be returned",
  "required": false,
  "type": "array",
  "items": {
    "type": "string",
    "enum": [
      "all",
      "contact",
      "emergency",
      "official",
      "person"
    ]
  }
}
```
- page_start
​
```JSON
{
  "name": "page_start",
  "in": "query",
  "description": "Which page to start on, Must be greater than 0",
  "required": false,
  "default": 1,
  "type": "integer",
  "format": "int32"
}
```
- page_size
​
```JSON
{
  "name": "page_size",
  "in": "query",
  "description": "Size of the collection returned, Must be greater than 0",
  "required": false,
  "default": 100,
  "type": "integer",
  "format": "int32"
}
```
​
## responses
Response objects can be defined at the root of the Open API 2.0 document under the `responses` object and in the `responses` object under the Operation object. If the response obect is common across Operation objects, you should define the Response object under the `responses` object at the root and reference that root definition under the `responses` object under the Operation object. Similar to the `parameters` object, just because you define the Response object under the root `responses` object does not automatically make it globablly applicable. You must still reference the root definition when specifying where it is applicable under the Operation object in a specific Path Item object.
​
Examples of Response objects that could be defined at the root level are HTTP error status code responses:
​
```JSON
"400": {
  "description": "Invalid Request - unable to interpret request",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
},
"401": {
  "description": "Authentication required to access data",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
},
"403": {
  "description": "Access denied to protected data",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
},
"404": {
  "description": "The resource does not exist",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
},
"405": {
  "description": "Method Not Allowed",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
},
"409": {
  "description": "Query parameters conflict or are malformed",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
},
"500": {
  "description": "Internal Server Error - Error occurred that wasn't the user's fault",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
},
"503": {
  "description": "Service Unavailable - Under construction",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
},
"504": {
  "description": "Gateway Timeout",
  "schema": {
    "$ref": "#/definitions/error_response"
  }
}
```
​
## securityDefinitions
All APIs should include one or more of the following OAuth2 Security Scheme objects. If scopes are available, then should be listed.
​
```JSON
"securityDefinitions": {
  "client_application_oauth": {
    "type": "oauth2",
    "tokenUrl": "https://api.byu.edu/token",
    "flow": "application",
    "scopes": {
      "write": "modify",
      "read": "read"
    }
  },
  "authorization_code_oauth": {
    "type": "oauth2",
    "authorizationUrl": "https://api.byu.edu/authorize",
    "tokenUrl": "https://api.byu.edu/token",
    "flow": "accesscode",
    "scopes": {
      "write": "modify",
      "read": "read"
    }
  },
  "implicit_code_oauth": {
    "type": "oauth2",
    "authorizationUrl": "https://api.byu.edu/authorize",
    "flow": "implicit",
    "scopes": {
      "write": "modify",
      "read": "read"
    }
  }
}
```
​
​
## security
The `security` object can be defined at the root of an Open API 2.0 document and under the Operation object. If a Security Requirement is specified at the root `security` object (different than the `parameters` and `responses` section), it **DOES** apply to the API globally.
​
## tags
Tags are not officially used in any capacity in BYU API services. Some tools utilize tag names in automating the generation of code, but there is no official recommendation for using tags and therefore use of them at any level is discouraged.
​
## externalDocs
If you have documentation that lives outside of the Open API 2.0 document and is accessible through a URL, you can reference the external documentation using the `externalDocs` object.
​
## x-mock_json
`x-mock_json` objects are an extension to the Open API 2.0 specification and are used for providing references to example data.
