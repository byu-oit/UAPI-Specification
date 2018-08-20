# University API Specification Best Practices

The [BYU University API Specification (UAPI)](https://github.com/byu-oit/UAPI-Specification/blob/master/University%20API%20Specification.md) is intended to simplify working with compliant APIs by providing a consistent interface and response format. While the specification covers a wide variety of issues related to providing and consuming APIs using the UAPI specification there are a number of items a provider could pay attention that will make the consumption of their API more convenient. This document is intended to help point out a number of those items. 

This document assumes a familiarity with the UAPI specification.

## Resources

The recommendations for naming resources are: 

- In general all resource names should be plural (e.g. persons vs person, students vs student). Sub-resources can be singular in the case that the sub-resource truly only represents a single instance of a sub-resource per top level resource. 

- All resources should be named using [snake\_case](https://en.wikipedia.org/wiki/Snake_case), specifically with all identifiers being lower case. 

## Properties 

- Property names should follow the convention used by resources - snake\_case with lower case for all identifiers. 

- Property names should be entered into [InfoHub](https://infohub.byu.edu) with definitions. 

- System properties that contain an identifer should have the type of identifier reflected in the name of the property (e.g. `updated_by_byu_id` instead of `updated_by_id`).

- Properties that represent identifers should resolve the identifier and use the most common representation as the values for `description` and `long_description`. The exception to this is when the resolution for the identifier appears in the same field\_set. 

- Properties that represent code values (e.g. `state`, `country` etc) should use the resolved value for the code value in the `description` and `long_description` elements. For example, for the property `country`, the `value` element may be `USA`, the `description` element would be `United States` and the `long_description` would be `United States of America`. 

- When a property's value is `null` the property object returned should omit the `value` element. The possibility of this happening should be indicated by the absense of the `required` tag in the corresponding swagger document. Once OpenAPI 3 (Swagger 3) is fully supported by our APIs the property will set the value to `null` (`nullable` properties are supported in OpenAPI 3). This helps differentiate between a property not having a value - `null` and having an empty value `""`. 

### HTTP Status codes 

consistent definitions for what is returned


### 

### Documentation

API documentation guidelines - information not contained in the Swagger

### Swagger 

What is required in swagger documents

 use `required` in swagger to designate which elements a client can expect. 


### HATEOAS Links

- Rel codes

- Services should create as much of the link as possible, e.g., `"related_resource": "https://api.byu.edu/byuapi/persons/v2/123456789"`. In circumstances that the entire link can't be automatically created (like if the API only has a PUT defined, but no POST), the service should return a proper templated URL using the [RFC 6570 format](https://tools.ietf.org/html/rfc6570) e.g. `"https://api.byu.edu/byuapi/persons/v2/{byu_id}"`, instead of something like `"https://api.byu.edu/byuapi/persons/v2/:byu_id"` (incorrect template according to RFC 6570) or `"https://api.byu.edu/byuapi/persons/v2/"` (helpful, but not as helpful as it could be).



## Client Side

HTTP Status codes, etc

SDKs and examples

