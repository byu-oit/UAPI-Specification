# BYU University API Standard

Specification Version 1.1   
Document Version 1.5

The BYU University API Standard is licensed under [The Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).

* Note - Unless otherwise specified the URLs contained in this document are for example purposes only. The actual URLs to any UAPI resource can be found [here](https://developer.byu.edu).

## Contents

- [1.0 Introduction](#10-introduction)
    - [1.1 University APIs vs Domain APIs](#11-university-apis-vs-domain-apis)
- [2.0 URLs](#20-urls)
    - [2.1 URL Design](#21-url-design)
        - [2.1.1 Top Level Resource](#211-top-level-resource)
        - [2.1.2 Sub-resource](#212-sub-resource)
        - [2.1.3 Resource (and Sub-resource) Identifier](#213-resource-and-sub-resource-identifier)
        - [2.1.4 Composite Resource Identifier](#214-composite-resource-identifier)
- [3.0 Resources](#30-resources)
    - [3.1 Resource Representation](#31-resource-representation)
    - [3.2 Representing a Single Resource](#32-representing-a-single-resource)
        - [3.2.1 Links](#321-links)
        - [3.2.2 Metadata](#322-metadata)
        - [3.2.3 Properties](#323-properties)
        - [3.2.4 Representing Property Values](#324-representing-property-values)
            - [3.2.4.1 Representing a Scalar Value](#3241-representing-a-scalar-value)
                - [3.2.4.1.1 Representing Dates and Times](#32411-representing-dates-and-times)
            - [3.2.4.2 Representing Arrays of Scalar Values](#3242-representing-arrays-of-scalar-values)
            - [3.2.4.3 Representing Complex Objects](#3243-representing-complex-objects)
            - [3.2.4.4 Representing Arrays of Complex Objects](#3244-representing-arrays-of-complex-objects)
            - [3.2.4.5 Special Considerations for Representing Complex Objects](#3245-special-considerations-for-representing-complex-objects)
        - [3.2.5 Representing Sub-resources](#324-representing-sub-resources)
        - [3.2.6 Example Single Resource Representation](#325-example-single-resource-representation)
        - [3.2.7 Example Single Sub-resource Representation](#326-example-single-sub-resource-representation)
    - [3.3 Representing a Collection of Resources](#33-representing-a-collection-of-resources)
        - [3.3.1 Collection Links](#331-collection-links)
        - [3.3.2 Collection Metadata](#332-collection-metadata)
        - [3.3.3 Values Array](#333-values-array)
        - [3.3.4 Sorted Collections](#334-sorted-collections)
            - [3.3.4.1 Sorted Collection Metadata](#3341-sorted-collection-metadata)
            - [3.3.4.2 Sorted Collection Query Parameters](#3342-sorted-collection-query-parameters)
        - [3.3.5 Large Collections](#335-large-collections)
            - [3.3.5.1 Collection Subsets Metadata](#3351-collection-subsets-metadata)
            - [3.3.5.2 Collection Subsets Query Parameters](#3352-collection-subsets-query-parameters)
            - [3.3.5.3 Collection Subsets HATEOAS Links](#3353-collection-subsets-hateoas-links) 
        - [3.3.6 Empty Collections](#336-empty-collections)
- [4.0 HATEOAS Links](#40-hateoas-links)
    - [4.1 Links](#41-links)
    - [4.2 Link Format](#42-link-format)
- [5.0 Sub-resources, Field\_sets, and Contexts](#50-sub-resources-field_sets-and-contexts)
    - [5.1 Field\_sets](#51-field_sets)
        - [5.1.1 Field\_sets Metadata](#511-field_sets-metadata)
        - [5.1.2 Field\_sets Query Parameter](#512-field_sets-query-parameter)
        - [5.1.3 The 'basic' field\_set](#513-the-basic-field_set)
        - [5.1.4 Field\_set Representation](#514-field_set-representation)
    - [5.2 Contexts](#52-contexts)
        - [5.2.1 Context Metadata](#521-context-metadata)
        - [5.2.2 Context Query String Parameter](#522-context-query-string-parameter)
        - [5.2.3 Context Representation](#523-context-representation)  
    - [5.3 Undefined Field\_set or Context parameter](#53-undefined-field_set-or-context-parameter)
- [6.0 Filters](#60-filters)
    - [6.1 Filter Parameters](#61-filter-parameters)
        - [6.1.1 Combining Filters](#611-combining-filters)
    - [6.2 Filtering Sub-resources](#62-filtering-sub-resources)
    - [6.3 Dot Notation (Filtering With Sub-resources)](#63-dot-notation-filtering-with-sub-resources)
    - [6.4 Wildcards](#64-wildcards)
    - [6.5 Comparison Operators](#65-comparison-operators)
- [7.0 Search](#70-search)
    - [7.1 Search Context And Filters](#71-search-context-and-filters)
    - [7.2 Search Metadata](#72-search-metadata)
    - [7.3 Search Query Parameters](#73-search-query-parameters)
    - [7.4 Search Results](#74-search-results)
- [8.0 Meta Data Sets](#80-meta-data-sets)
    - [8.1 Meta Data APIs](#81-meta-data-apis)
    - [8.2 Domain Meta Data](#82-domain-meta-data)
        - [8.2.1 Examples of Domain Meta Data](#821-examples-of-domain-meta-data)
    - [8.3 Authorization](#83-authorization)
    - [8.4 Errors](#84-errors)
- [9.0 Files](#90-files)
    - [9.1 File Downloads](#91-file-downloads)
        - [9.1.1 File Metadata](#911-file-metadata)
    - [9.2 File Uploads](#92-file-uploads)
        - [9.2.1 Updating File Metadata](#921-updating-file-metadata)
- [10.0 HTTP POST, PUT, DELETE](#100-http-post-put-delete)
    - [10.1 PUT](#101-put)
        - [10.1.1 Resource Creation Using PUT](#1011-resource-creation-using-put)
    - [10.2 POST](#102-post)
    - [10.3 DELETE](#103-delete)
    - [10.4 Response Representation](#104-response-representation)
- [11.0 Authorization](#110-authorization)
    - [11.1 Field\_set Authorization](#111-field_set-authorization)
        - [11.1.1 Data Classification](#1111-data-classification)
    - [11.2 Top Level Resource Authorization](#112-top-level-resource-authorization)
    - [11.3 Property Authorization](#113-property-authorization)
    - [11.4 Filter Authorization](#114-filter-authorization)
    - [11.5 Authorization Failures](#115-authorization-failures)
        - [11.5.1 Top Level Resource Authorization Failure](#1151-top-level-resource-authorization-failure)
        - [11.5.2 Sub-resource Authorization Failure](#1152-sub-resource-authorization-failure)
        - [11.5.3 Partial Authorization Failure](#1153-partial-authorization-failure)
    - [11.6 Restricted Resources](#116-restricted-resources)
        - [11.6.1 Resource Metadata](#1161-resource-metadata)
        - [11.6.2 Authorized Access](#1162-authorized-access)
        - [11.6.3 Unauthorized Access Attempts](#1163-unauthorized-access-attempts)
- [12.0 Errors](#120-errors)
    - [12.1 HTTP Status Codes](#121-http-status-codes)
    - [12.2 Error Response Format](#122-error-response-format)
        - [12.2.1 validation\_response](#1221-validation_response)
        - [12.2.2 validation\_information](#1222-validation_information)
    - [12.3 Top Level Resource Errors](#123-top-level-resource-errors)
        - [12.3.1 Single Top Level Resource Errors](#1231-single-top-level-resoure-errors)
        - [12.3.2 Top Level Resource Collection Errors](#1232-top-level-resource-colletion-errors)
    - [12.4 Sub-resource Errors](#124-sub-resource-errors)
    - [12.5 Partial Error Response](#125-partial-error-response)
    - [12.6 Special Error Situations](#126-special-error-situations)
        - [12.6.1 `404 Not Found` Errors](#1261-404-not-found-errors)
        - [12.6.2 Invalid Query Parameters](#1262-invalid-query-parameters)
- [Version History](#version-history)   


## 1.0 Introduction

The University API (UAPI) Specification is intended to bring standardization to consumers and producers of APIs. It presents a standard for URL design, request and response format, and HTTP methods and return codes. The UAPI standard is required for any API designated as part of the University API and recommended for other APIs whenever feasible. 

### 1.1 University APIs vs Domain APIs

The University has designated a number of common resources as part of the UAPI implementation such as students, employees, and persons. Other resources represent  specific constructs useful within a specific business domain. It is recommended where possible to apply the UAPI standard to domain specific APIs. Detailed information for applying the UAPI standard to domain APIs is provided in a separate document. 

## 2.0 URLs

URLs provide a unique address space for resources within the UAPI. The UAPI specifies a format for URLs to ensure consistency across resources. 

### 2.1 URL Design

A URL that is part of the UAPI implementation will have the following format:

`https://api.byu.edu/byuapi/resource[/identifier]`

If the resource has sub-resources (e.g. persons have addresses) they are addressed as follows:

```
    https://api.byu.edu/byuapi/<top-level-resource>/<top-level-resource-identifier>/<sub-resource>/<sub-resource-identifier>
```

#### 2.1.1 Top Level Resource

The resource portion of the URL represents an entity within the UAPI. For example, the resource "persons" contains  data related to a person. The resource `https://api.byu.edu/byuapi/persons` is the URL for the Persons resource. 

Resources that appear first in the URL structure are considered top-level resources.

All resources are written in their plural form. There are a few exceptions where the API represents a single instance of a resource but these are rare. 

#### 2.1.2 Sub-resource

Resources are "sub-resources" if they are part of a top level resource. For example, addresses are sub-resources of a persons resource because a person has addresses associated with them. 

A sub-resource can be addressed as part of the top level resource by adding the name of the sub-resource to the URL. For example,

`https://api.byu.edu/byuapi/persons/123456789/addresses`

is the URL for the addresses associated with the person identified by the byu_id of 123456789.

#### 2.1.3 Resource (and Sub-resource) Identifier

If a resource represents a collection of data an item in that resource collection should be addressable by adding its identifier to the URL. For example, the URL `https://api.byu.edu/byuapi/persons/123456789` represents the person with the byu_id of 123456789. An HTTP GET to this URL would return the data related to this particular person.   

Sub-resources can also have identifiers. The URL for the work address of a person would be `https://api.byu.edu/byuapi/persons/123456789/addresses/WRK`

#### 2.1.4 Composite Resource Identifier

Some resources and sub-resources must be identified by using more than one piece of information. This type of identifier is termed a *composite identifier*. A composite identifier uniquely identifies a resource by using multiple pieces of information. The values in a composite identifier should be separated by a comma. 

Composite identifiers should conform to the pattern:

`https://api.byu.edu/byuapi/resource/{key}[,{additional_keys}]`

An example of a URL using a composite identifier is: 

`https://api.byu.edu/byuapi/classes/2016Fall,MATH,110,001`

## 3.0 Resources 

Resources have properties associated with them. Some properties are simple name / value pairs and others may be complex objects. Sub-resources are an example of complex objects that are properties of a top-level resource.  

### 3.1 Resource Representation

When a consumer of an API interacts with a resource the representation of the properties of that resource is dependent upon the mime-type used. All text based resources will be represented as JSON using the `application/json` mime-type. Other text representations (XML, etc) can be provided if the API is capable. The HTTP `Accept` header should be included in the request to indicate to the API which representation(s) the consumer is capable of processing and the HTTP `Content-Type` header should be included in the response indicating the mime-type of the data being represented.  Binary resource properties such as images should use the appropriate mime-type for the type of data being represented. 

### 3.2 Representing a Single Resource

Single resources are addressed by URLs that end in identifiers (e.g. `https://api.byu.edu/byuapi/persons/123456789`). 

The resource should always include the following elements:

* Links
* Metadata
* One or more sub-resources represented as field\_sets (see [5.1 field\_sets](#51-field_sets)).

Top level resources should return a default set of properties when no sub-resources have been requested. These properties should be included in a single JSON object with the name `basic`. See [5.1.3 The Basic Field\_set](#513-the-basic-field_set) for more information. 

Multiple sub-resources can be requested in a single HTTP request. See [5.0 Sub-resources, Field\_sets, And Contexts](#50-sub-resources-field_sets-and-contexts) for more information.

#### 3.2.1 Links

The links property is an object that represents possible future actions that can be performed against this resource. The contents of the links section may vary depending upon the context in which the resource is being accessed (date, user authorizations, etc) See [4.0 HATEOAS Links](#40-hateoas-links) for a more complete discussion of HATEOS links and their purpose.  

#### 3.2.2 Metadata

The metadata property contains data about the request for this resource including any errors the request has generated. In the case of a request that returns a single resource the metadata may include the following:

|Property|Required|Description
|-----|-----|-----
|validation_response|yes|This is an object that contains two required properties: *code* and *message*.
|validation_information|no|This is an array of strings that provide information about errors correlated to the `validation_response.code` and HTTP response code. See [10.0 HTTP POST, PUT, DELETE](#100-http-post-put-delete) for more information. 
|cache|required if the result is a cached value|This is an object that contains one required property: *date_time*. The date_time value is when the data was updated in the cache.
|restricted|Required if the resource deals with an individual|Indicates that the resource represents a person that has requested that their records be restricted. Special rules apply to restricted resources. See [11.6 - Restricted Resources](#116-restricted-resources) for more information. 


Metadata related to [field\_sets](#51-field_sets) and [contexts](#52-contexts) along with any custom metadata about the resource may also be included.

The following metadata object would indicate a successful request for this resource: 

```json
    "metadata": {
      "restricted" : false,
      "validation_response": {
        "code": 200,
        "message": "Success"
      }
    }
```

The following metadata would indicate an error occurred when processing the request:

```json
"metadata": {
  "restricted" : false,
  "validation_response": {
    "code": 403,
    "message": "Not Authorized"
  },
  "validation_information": [
    "Not authorized"
  ]
 }
```
See [12.0 Errors](#120-errors) for more information on error handling.

#### 3.2.3 Properties

Resource properties contain information about the state of a resource. The persons resource has properties such as byu\_id, net\_id, preferred name, etc. Property values are returned in a UAPI specific format that provides additional information for the consumer about the property. The The UAPI format is a JSON object that has the following elements:

|Element|Required|Description
|-----|-----|-----
value \| value_array \| object \| object_array|One and only one is required|Contains the data value to be processed. See individual sections for a definition of the possible representations.
|api\_type|Yes|Describes how this value may be used in this API. For a list of the possible values for api\_type and the rules associated with each value see the list below.
|key|Required if the property is the identifier or one of the composite identifiers of the resource.|Designates that the property is one of the key elements for this resource. Key fields are required to have values - they are not allowed to be blank or null. 
|description|No|Explains the data value in human-friendly terms. Should be limited to 30 characters. 
|long\_description|No|Explains the data value in human-friendly terms; contains more information than the (short) description. Should be limited to 256 characters. 
|display\_label|No|Provides a suggested string to use when creating a label for this property in the user interface. Should be limited to 30 characters. 
|domain|Required if the value property is part of a set of allowable values.|Contains the URL that can be used to retrieve the set of allowable values. The result of invoking the URL could be used to populate the UI's. For example the value `"domain": "https://api.byu.edu/byuapi/meta/classes/year_terms"` may return a set of the valid year terms. See [8.0 Meta Data Sets](#80-meta-data-sets) for more information. 
|related\_resource|Required if the api\_type property is `related`.|If the api\_type is `related` this property will contain the resource-name that "owns" this property. The resource-name can be used to find a HATEOAS link that can access this property.

The `api_type` field is required for all properties. It indicates more information about the value being returned such as if it is modifiable by the consumer, a derived field, a system generated value, etc. 

The possible values for `api_type` are:

|api\_type|Modifiable by the consumer through this API|Description
|-----|-----|-----
read-only|No|There are either business rules or authorization considerations that do not allow the property to be changed at this time by the consumer.
|modifiable|Yes|The property may be modified by the consumer.
|system|No|The value was assigned by the system. A date-time field that tracks the last time the resource was updated is a good example of a system data value.
|derived|No|The property was derived by a "calculation" on other properties. An example might be GPA.
|unauthorized|No|The consumer is not authorized to retrieve this property. **This api\_type is deprecated. It should not be used for ANY new development.** See [11.0 Authorization](#110-authorization) for more information. 
|related|No|The responsibility for manipulating this property doesn't belong to this API. The business logic to modify the property exists in the `related_resource`.

#### 3.2.4 Representing Property Values

Property values can be expressed as either singleton scalar values, arrays of scalar values, complex objects, or arrays of complex objects.

##### 3.2.4.1 Representing a Scalar Value
The `value` element is used to represent a single scalar as the value of the property. The value returned can be `null` if the data type of the property supports it.  

A property with a single scalar value would look something like this:  

```json
"byu_id": {
  "api_type": "system",
  "display_label": "BYU ID",
  "key": true,
  "value": "123456789",
  "description": "Joe Student"
},
```

###### 3.2.4.1.1 Representing Dates And Times

Date and Date/Time data types should be represented as strings using the [RFC-3339](https://tools.ietf.org/html/rfc3339) format. It is suggested that UTC be used instead of local time zones. 

##### 3.2.4.2 Representing Arrays of Scalar Values

The `value_array` element is used to represent an array of scalar values as the value of the property. The array is represented as a JSON array. The elements for the property are divided between those that apply to the property as a whole and those that are specific to the entries in the array. The `value_array` element cannot be `null`. If the array has no elements the array an empty array should be returned. 

|Element|Applies to Property or Array Entry|Note
|-----|-----|-----
|api\_type|Property|
|display\_label|Property|
|description|Entry|The description of the individual entry in the array.
|long\_description|Entry|
|domain|Property|
|related|Both|If applied to the property the URL represents the resource to use to interact with the entire array. If applied to the entry the URL should contain the reference to this specific entry in the array.
|key|Not Allowed|Arrays cannot be part of the primary key of a resource.

```json
"instructor_byu_ids": {
    "api_type": "related",
    "display_label": "Instructors",
    "related": "https://api.byu.edu/classes/20185,cs142,1",
    "value_array": [
        {
            "value": "123456789",
            "description": "Joe Instructor",
            "api_type": "related",
            "related": "https://api.byu.edu/persons/123456789"
        },
        {
            "value": "987654321",
            "description": "Jane Instructor",
            "api_type": "related",
            "related": "https://api.byu.edu/persons/987654321"
        }
    ] 
}
```

##### 3.2.4.3 Representing Complex Objects

The `object` element is used to represent a single complex JSON object as the value of the property. The object is represented in standard UAPI specification format as described in [3.2.3 Properties](#323-properties). Only certain elements are valid for a property that contains an object. The `object` element can be `null`. 

|Element|Note|
|-----|-----|
|api\_type|Must be either `read_only` or `related`.
|display\_label|May be used to indicate a `display_label` for the entire object.
|related|If applied to the property the URL represents the resource to use to interact with the object as a whole. 

An example of an property containing an object is as follows:

```json
"final_exam_schedule": {
    "object": {
        "building": {
            "value": "RB",
            "description": "Richards Building",
            "api_type": "related",
            "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/FINAL%20EXAM,1",
            "domain": "https://api.byu.edu/byuapi/meta/classes/buildings"
        },
        "room": {
            "value": "164",
            "api_type": "related",
            "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/FINAL%20EXAM,1"
        },
        "days": {
            "value": "S",
            "api_type": "related",
            "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/FINAL%20EXAM,1"
        },
        "start_time": {
            "value": "11:00",
            "api_type": "related",
            "description": "11:00am",
            "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/FINAL%20EXAM,1"
        },
        "end_time": {
            "value": "14:00",
            "api_type": "related",
            "description": "2:00pm",
            "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/FINAL%20EXAM,1"
        },
        "start_date": {
            "value": "2018-12-15",
            "api_type": "related",
            "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/FINAL%20EXAM,1"
        },
        "end_date": {
            "value": "2018-12-15",
            "api_type": "related",
            "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/FINAL%20EXAM,1"
        }
    },
    "api_type": "read-only"
},
```

##### 3.2.4.4 Representing Arrays of Complex Objects

The `object_array` element is used to represent an array of complex JSON objects as the value of the property. The objects are represented in standard UAPI specification format as described in [3.2.3 Properties](#323-properties). Only certain elements are valid for a property that contains an array of objects. An `object_array` element cannot be `null`. If there are no objects to in the array then an empty array should be returned. 

|Element|Note
|-----|-----
|api\_type|Must be either `read-only` or `related`.
|display\_label|May be used to indicate a `display_label` for the entire array of objects.
|related|If applied to the property the URL represents the resource to use to interact with the object array as a whole. 

An example of an property containing an array of objects is as follows:

```json
"when_taught": {
    "object_array": [
        {
            "building": {
                "value": "RB",
                "description": "Richards Building",
                "api_type": "related",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,3",
                "domain": "https://api.byu.edu/byuapi/meta/classes/buildings"
            },
            "room": {
                "value": "206",
                "api_type": "related",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,3"
            },
            "days": {
                "value": "MW",
                "api_type": "related",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,3"
            },
            "start_time": {
                "value": "12:00",
                "api_type": "related",
                "description": "12:00pm",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,3"
            },
            "end_time": {
                "value": "12:50",
                "api_type": "related",
                "description": "12:50pm",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,3"
            },
        },
        {
            "building": {
                "value": "RB",
                "description": "Richards Building",
                "api_type": "related",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,2",
                "domain": "https://api.byu.edu/byuapi/meta/classes/buildings"
            },
            "room": {
                "value": "164",
                "api_type": "related",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,2"
            },
            "days": {
                "value": "F",
                "api_type": "related",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,2"
            },
            "start_time": {
                "value": "12:00",
                "api_type": "related",
                "description": "12:00pm",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,2"
            },
            "end_time": {
                "value": "12:50",
                "api_type": "related",
                "description": "12:50pm",
                "related_resource": "https://api.byu.edu/byuapi/classes/v2/Fall2018,C%20S,301R,003/assigned_schedules/CLS,2"
            },
        }
    ],
    "api_type": "read-only"
},
          
```

##### 3.2.4.5 Special Considerations For Representing Complex Objects

While the UAPI specification supports complex objects (`value_array`, `object`, and `object_array`) they should be used judiciously. There are a number of implications to using complex objects including:

- Lack of addressability - Complex objects cannot be addressed directly the same way sub-resources can. 
- Lack of selectability - Complex objects are always included in the field\_set that contains them, unlike field\_sets in general which can be included at the discretion of the consumer. 
- Data classification issues - Like field\_sets, complex objects inherit the classification of the most restricted property they contain. This classification expands to include the entire field\_set that contains the complex object.  



#### 3.2.5 Representing Sub-resources

Single value sub-resources (i.e. those retrieved by using an identifier in the URL) are represented much the same way the `basic` object is represented. They require the same links, metadata, and properties elements. All properties are represented just under the root of the returned object (i.e. they do not have a `basic` object).

#### 3.2.6 Example Single Resource Representation

A single top level resource representation would look like:

```json
{
    "links":...,
    "metadata":..., 
    "basic": {
        "links": {
            "persons__info": {
                "rel": "self",
                "href": "https://api.byu.edu/byuapi/persons/123456789",
                "method": "GET"
            },
            "students__info": {
                "rel": "students__info",
                "href": "https://api.byu.edu/byuapi/students/123456789",
                "method": "GET"
            }
        },
        "metadata": {
            "restricted" : false,
            "validation_response": {
                "code": 200,
                "message": "Successful"
            }
        },
        "byu_id": {
            "value": "123456789",
            "api_type": "system",
            "key": true
        },
        "person_id": {
            "value": "987654321",
            "api_type": "system"
        },
        "net_id": {
            "value": "joe",
            "api_type": "related",
            "related_resource": "https://api.byu.edu/byuapi/persons/123456789/credentials/NET_ID,joe"
        },
        "personal_email_address": {
            "value": "joe@byu.edu",
            "api_type": "related",
            "related_resource": "https://api.byu.edu/byuapi/persons/123456789/email_addresses/PERSONAL"
        },
        "primary_phone_number": {
            "value": "",
            "api_type": "related",
            "related_resource": "https://api.byu.edu/byuapi/persons/123456789/phones"
        },
        "date_time_updated": {
            "value": "2016-09-21T09:03:18.000Z",
            "api_type": "system"
        },
        "updated_by_id": {
            "value": "323232323",
            "description": "Joe Admin",
            "api_type": "system"
        },
        "date_time_created": {
            "value": "1997-02-07T12:22:32.000Z",
            "api_type": "system"
        }
    }
}
```

#### 3.2.7 Example Single Sub-resource Representation 

A single sub-resource representation would look like:

```json
{
    "links": {
        "group_memberships__info": {
            "rel": "self",
            "href": "https://api.byu.edu/byuapi/persons/123456789/group_memberships/ADMINISTRATIVE",
            "method": "GET"
        },
        "group_memberships__modify": {
            "rel": "group_memberships__modify",
            "href": "https://api.byu.edu/byuapi/persons/123456789/group_memberships/ADMINISTRATIVE",
            "method": "PUT"
        },
        "group_memberships__delete": {
            "rel": "group_memberships__delete",
            "href": "https://api.byu.edu/byuapi/persons/123456789/group_memberships/ADMINISTRATIVE",
            "method": "DELETE"
        }
    },
    "metadata": {
        "restricted" : false,
        "validation_response": {
            "code": 200,
            "message": "Success"
        },
        "validation_information": [
            "No additional information"
        ]
    },
    "group_id": {
        "value": "ADMINISTRATIVE",
        "description": "Administrative",
        "api_type": "read-only",
        "key": true
    },
    "group_type": {
        "value": "A",
        "api_type": "read-only"
    },
    "byu_id": {
        "value": "123456789",
        "description": "Joe Admin",
        "api_type": "system",
        "key": true
    },
    "department": {
        "value": "OIT- Administration",
        "api_type": "related",
        "related_resource": "https://api.byu.edu/byuapi/employees",
        "domain": "https://api.byu.edu/byuapi/meta/employees/departments"
    }
}
```

### 3.3 Representing a Collection of Resources

If a UAPI resource (top level or sub-resource) is accessed with no identifier or via a filter or search (see [6.0 Filters](#60-filters) and [7.0 Search](#70-search)) a collection of individual resource representations is returned. This collection will have its own set of links and metadata with values that pertain to the collection. The individual resource representations as defined in [3.1 Resource Representation](#31-resource-representation) are returned in a JSON array named `values`. 

#### 3.3.1 Collection Links

The links object for a collection will contain [HATEOAS](#40-hateoas-links) links related to processing the collection. They may include links for paging, etc.  

#### 3.3.2 Collection Metadata

The metadata associated with collections includes data about the collection that has been returned. 

Metadata related to [field_sets](#51-field_sets) and [contexts](#52-contexts) along with any custom metadata about the resource collection may also be included. 

|Property|Required|Description
|-----|-----|-----
|validation\_response|yes|This is an object that contains two required properties: *code* and *message*.
|validation\_information|no|This is an array of strings that provide information about errors correlated to the `validation_response.code` and HTTP response code.
|cache|required if the result is a cached value|This is an object that contains one required property: `date_time`. The `date_time` value is when the data was updated in the cache.
|restricted|Required for sub-resource collections that deal with individuals. Not required for top level resource collections.|Indicates that the sub-resource represents some aspect of a person that has requested that their records be restricted. See [11.6 - Restricted Resources](#116-restricted-resources) for more information.
|collection\_size|required|The number of items of the resource which exist in the entire collection.

The metadata returned for a resource collection would look like:

```json
"metadata": {
  "restricted": false,  
  "validation_response": {
    "code": 200,
    "message": "Success"
  },
  "collection_size": 150
 }
```

#### 3.3.3 Values Array

The `values` array contains an entry for each individual resource representation. The representations must follow the specification in [3.1 Resource Representation](#31-resource-representation) including containing the `links` and `metadata` properties. 

#### 3.3.4 Sorted Collections

An API can optionally support the ability for a consumer to request the results in a collection be returned in a specific order. Results of the request will be sorted before applying any subset parameters specified in the request (see [3.3.5 Large Collections](#335-large-collections)).

##### 3.3.4.1 Sorted Collection Metadata 

The following `metadata` properties are required if sorting is supported by the resource: 

|Property|Description
|-----|-----
|sort\_properties\_available|The properties that can be used in requesting a specific order of the collection results.
|sort\_properties\_default|The default properties used to sort the collection.
|sort\_order\_default|The default sort order used to sort the collection. 

Sort metadata would look like the following:

```json
    "metadata": {
        "sort_properties_available": [
            "byu_id",
            "net_id",
            "sort_name"
        ],
        "sort_properties_default": [
            "sort_name",
            "net_id"
        ],
        "sort_order_default": "ascending"
    },
```
##### 3.3.4.2 Sorted Collection Query Parameters

The query parameters used to specify sort preferences are as follows: 

|Parameter|Description
|-----|-----
|sort\_properties|Sort the result by the properties listed. Multiple properties may be listed (comma separated). If a property is listed that does not appear in the `sort_properties_available` metadata property a `400 Bad Request` HTTP status code should be returned for the request. 
|sort\_order|Specify the order for the sort, either `ascending` or `descending`. Only a single order can be specified. The default is `ascending`.

#### 3.3.5 Large Collections

Some resources and sub-resources represent a large number of instances. APIs that support large numbers of instances should implement subsets in order to provide the consumer a way to move through the collection in a reasonable way. The consumer should be aware that it may be possible that the underlying data has changed between invocations so instances may be repeated or skipped. 

To implement subsets a resource must include specific `metadata` and `links` with the collection in the response. 

Specifying the start and size of the next subset of results is done via metadata properties. Consumers can specify an offset from the beginning of the collection or the primary key of the instance to start with.

##### 3.3.5.1 Collection Subsets Metadata

The following `metadata` properties are required if subsets are supported by the resource: 

|Property|Description
|-----|-----
|default\_subset\_size|Unless overridden in query parameters, this endpoint will return this number of resources in each subset.
|max\_subset\_size|The largest number of resources this API can return in one subset.
|subset\_start|The offset (zero based) from the beginning of the collection of the first instance in this subset.
|subset\_size|The number of instances in this subset. 

The metadata returned for a resource collection that supports subsets would look like:

```json
"metadata": {
  "restricted": false,  
  "validation_response": {
    "code": 200,
    "message": "Success"
  },
  "collection_size": 150,
  "default_subset_size": 50,
  "max_subset_size": 1000,
  "subset_size": 50,
  "subset_start": 51
 }
```

##### 3.3.5.2 Collection Subsets Query Parameters

In order to implement subsets the following query string parameters are used:

|Parameter|Description
|-----|-----
|subset\_start_offset|The offset for the first resource in the collection to be returned in this subset.
|subset\_size|The number of resources in the collection to return (defaults to the value of `max_subset_size`).
|subset\_start\_key|The primary key of the resource to start the subset with. Optional alternative to `subset_start_offset`. 

If both `subset_start_offset` and `subset_start_key` are specified in the same query string a `400 Bad Request` should be returned for the entire request. 

##### 3.3.5.3 Collection Subsets HATEOAS Links

Additional `links` are included that provide the link to use in order to move within the collection:

|Link|Content
|-----|-----
|*resource-name*\_\_info|Link to retrieve this collection - see [4.0 HATEOAS Links](#40-hateoas-links)
|*resource-name*\_\_first|Link to the first subset of the collection
|*resource-name*\_\_current|Link to the current subset in the collection
|*resource-name*\_\_last|Link to the last subset of the collection
|*resource-name*\_\_previous|Link to the previous subset in the collection
|*resource-name*\_\_next|Link to the next subset in the collection

An example of the subset links for the persons resource would be as follows:

```json
        "persons__info": {
            "rel": "self",
            "href": "https://api.byu.edu/byuapi/persons/",
            "method": "GET"
        },
        "persons__first": {
            "rel": "persons__first",
            "href": "https://api.byu.edu/byuapi/persons/?subset_start=0,subset_size=100",
            "method": "GET"
        },
        "persons__last": {
            "rel": "persons__last",
            "href": "https://api.byu.edu/byuapi/persons/?subset_start=345600,subset_size=100",
            "method": "GET"
        },
        "persons__current": {
            "rel": "persons__current",
            "href": "https://api.byu.edu/byuapi/persons/?subset_start=300,subset_size=100",
            "method": "GET"
        },
        "persons__next": {
            "rel": "person__next",
            "href": "https://api.byu.edu/byuapi/persons/?subset_start=400,subset_size=100",
            "method": "GET"
        },
        "persons__previous": {
            "rel": "persons__previous",
            "href": "https://api.byu.edu/byuapi/persons/?subset_start=200,subset_size=100",
            "method": "GET"
        },
```

#### 3.3.6 Empty Collections

If the collection returned is empty (e.g. no resources matched a filter) the collection metadata should still be returned. If subsets are supported subset data should still be valid - `subset_start` should be `0`, `subset_size` should also be `0` since no values were returned. The `values` array should be empty. 

## 4.0 HATEOAS Links

Hypertext As The Engine Of Application State (HATEOAS) is one of the fundamental design considerations of a REST based API. HATEOAS uses hypertext links to provide the consumers of an API with information regarding the next allowable operations the consumer is allowed to perform. 

### 4.1 Links

HATEOAS links are intended to convey to the consumer the next possible actions that can be performed on a resource or collection of resources. In the case of a collection of resources the `links` property may include links for retrieving the first, next, and last resources in the collection and adding a new value into the collection along with paging through the collection. For a single resource the `links` property may include links to modify or delete the resource along with a link to retrieve this specific resource (useful if the resource is returned as part of a collection). Links should only be provided if the consumer is allowed to perform the specific action represented by the link. For example if the consumer is allowed to modify but not delete the current person resource the `persons__modify` link will be present in the `links` property while the `persons__delete` will not. This way the consumer does not need to understand the business logic behind authorization, it can assume an operation is possible by the presence of the corresponding link. 

### 4.2 Link Format

The UAPI standard varies from traditional HATEOAS implementations in order to make things easier for consumers to utilize the `links` property. HATEOAS links in the UAPI have the following format:

```json
 "persons__info": {
                "rel": "self",
                "href": "https://api.byu.edu/byuapi/persons/123456789",
                "method": "GET"
            },
```

The name each of link object and the `rel` property follows the defined pattern `resource-name__business-action`.   Business actions should convey operations that make sense within the business domain. Consumers can use the name of the links to determine which operations can be performed on the resource. 

The properties of the link are defined as follows:  

|Property|Description
|-----|-----
|rel|The `rel` property defines the relationship the link has relative to the resource. The UAPI specification requires the value of `rel` either be `self` or match the name of the HATEOAS link object name. `self` is a special value for `rel` that indicates this link can be used to retrieve the current resource. 
|href|The href contains the URL used to perform the HATEOAS link action (this may also be a templated URL according to RFC 6570).
|method|The method contains the HTTP method used when invoking the URL in the href property.

The UAPI specification requires a `self` link be included. This link should be the link that can be used to retrieve this specific resource (or collection). For a top-level resource it should be named `resource-name__info`. If the link is for a portion of the result (such as a sub-resource) it should be named `sub-resource-name__info`.

An example links object for the person resource at `https://api.byu.edu/byuapi/persons/123456789` would look something like:

```json
          "links": {
            "basic__info": {
                "rel": "self",
                "href": "https://api.byu.edu/byuapi/persons/123456789",
                "method": "GET"
            },
            "basic__modify": {
                "rel": "basic__modify",
                "href": "https://api.byu.edu/byuapi/persons/123456789",
                "method": "PUT"
            },
            "basic__delete": {
                "rel": "basic__delete",
                "href": "https://api.byu.edu/byuapi/persons/123456789",
                "method": "DELETE"
            }
```

## 5.0 Sub-resources, Field\_sets, and Contexts

Access to individual sub-resources is accomplished by adding the desired sub-resource name and an optional identifier to the URL of the top level resource. There are cases where access to more than one sub-resource in a single request is necessary or desirable. The UAPI standard provides two query string parameters to this possible: `field_sets` and `contexts`.

### 5.1 Field\_sets

Field\_sets are merely a convenient way to request and represent multiple sub-resources in a single request to a top level resource. The names of field\_sets and sub-resources must be the same. If an API supports sub-resources it must also support field\_sets. 

#### 5.1.1 Field\_sets Metadata

The API should document the field\_sets supported in the metadata of the top level resource. The metadata properties are:

|Property|Required|Description
|-----|-----|-----
|field\_sets\_returned|only if specific field\_sets have been requested|JSON array of all the field\_sets represented in this result. May be different from the list of field\_sets requested in the query string.
|field\_sets\_available|only if field\_sets are supported|JSON array of all available field\_sets available to request for this resource. A consumer may not have access to all field\_sets in this array.  
|field\_sets\_default|only if field\_sets are supported| The field\_sets that will be returned if no `field_sets` query parameter is specified. 
 
 Example `metadata` from a top-level resource that supports field\_sets looks like:
 
```json
    "metadata": {
        "field_sets_returned": [
            "basic"
        ],
        "field_sets_available": [
            "basic",
            "addresses",
            "email_addresses",
            "languages",
            "phones",
            "relationships"
        ],
        "field_sets_default": [
            "basic"
        ],
    },
```

#### 5.1.2 Field\_sets Query Parameter

To specify one or more field\_sets to be returned the `field_sets` query string parameter is used to provide a comma separated list the field\_sets being requested. 

#### 5.1.3 The 'basic' Field\_set

The UAPI specification defines a special field\_set that doesn't directly correspond to a sub-resource. The  `basic` field\_set is used to represent a set of properties the resource will return when no field\_sets are requested. These properties can include items from sub-resources as well as the top-level resource. If the query string for the request includes the `field_sets` or `contexts` parameter the `basic` field\_set will NOT be included in the result unless it is part of the list of requested field\_sets. 

#### 5.1.4 Field\_set Representation

Sub-resources requested using the `field\_sets` query string parameter are represented as JSON objects at the top level of the individual resource representation. The name of the object is the name of the sub-resource. The representation of each sub-resource is exactly the same as if the sub-resource was accessed directly from the URL. 

A request to `https://api.byu.edu/byuapi/persons/123456789?field_sets=basic,addresses` would look like: 

```json
{
    "links":{...},
    "metadata":{...},
    "basic": {...},
    "addresses": {...}
}
```

### 5.2 Contexts

Contexts are an optional part of the UAPI specification that allow APIs with a large number of field\_sets to combine them into logical groups. This allows for simplification of the URL query string. Contexts should reflect the business domain related to the top-level resource. Field\_sets can be included in multiple contexts. 

#### 5.2.1 Context Metadata

If a top-level resource supports contexts it should add the `contexts_available` metadata to the field\_set metadata. The value of this property is a JSON object consisting of a JSON array for each of the available contexts. The JSON array will contain the field\_sets included in that context. 

An example of the context metadata is as follows: 

```json
        "contexts_available": {
            "all": [
                "basic",
                "addresses",
                "email_addresses",
                "languages",
                "phones",
                "relationships"
            ],
            "contact": [
                "basic",
                "addresses",
                "email_addresses",
                "phones"
            ],
            "person_bio": [
                "basic",
                "languages"
            ]
```

#### 5.2.2 Context Query String Parameter

To specify a context or contexts to be returned the `contexts` query string parameter is used to list the contexts being requested. For example, using the above example context definitions, the query string parameter `contexts=contact,person_bio` would return the `basic`, `addresses`, `email_addresses`, `phones`,  and `languages` field\_sets. 

#### 5.2.3 Context Representation

Responses generated by using the `contexts` query string parameter are represented in exactly the same way as if the consumer had requested the included field\_sets using the `field_sets` query string parameter. If the consumer specifies both the `contexts` and `field_sets` query string parameters the result should be a union of the field\_sets requests. A field\_set will only be included once in the result. 

### 5.3 Undefined Field\_set Or Context Parameter

If the query string of a request contains an undefined field\_set or context the entire request should be rejected with a HTTP status code of `400 Bad Request`. A `metadata` property should be returned in the response body that contains the `validation_response`. `validation_information` should also be returned containing a message or messages indicating which query string parameter is the cause of the error. 

## 6.0 Filters
 
 A collection of resources can be filtered using query string parameters. Which of the properties of a resource can be used as filters in the query string is left to the API to define and should follow the business use cases of the top-level resource and follow any authorization rules for the property (see [11.4 Filter Authorization](#114-filter-authorization)). For example our `persons` top level resource uses `byu_id` as its primary key. Unfortunately `byu_id` is only one of three keys in common use to access the `persons` resource. If a consumer wants to find a person with a specific `net_id` then a query string parameter of `net_id` should be supported by the API. Likewise it makes sense to allow for filtering on other properties such as `email_address` or `phone_number`.  Information in the Swagger definition for the API should include which properties can be used for filtering. 
 
#### 6.1 Filter Parameters 
 
Filter parameters given in the query string should correspond to properties provided by the resource and should be defined in the Swagger definition of the resource. The `field_sets` and `contexts` query string parameters can be combined with filter parameters to customize the resulting representation. 

##### 6.1.1 Combining Filters

Multiple filters can be combined to create more complex filtering rules. APIs can also support multiple values per filter parameter (comma separated) as well. When multiple values are specified in a filter parameter the values are "OR"ed together. Multiple filter query string parameters are "AND"ed together. 
 
#### 6.2 Filtering Sub-resources
 
 Filters apply to the lowest level resource specified on the URL. If the URL only specifies a top level resource a collection of top level resources that match the filter will be returned. If the URL includes a sub-resource the filter will be applied to that sub-resource and the collection returned will be instances of the sub-resource. For example, the URL `https://api.byu.edu/byuapi/persons?net_id=johndoe` will return a collection of persons that have a `net_id` of `johndoe`. The URL `https://api.byu.edu/byuapi/persons/123456789/addresses?address_type=MAL` will return a collection of all mailing addresses associated to the person with the byu_id `123456789` that have an `address_type` of `MAL`. 
 
#### 6.3 Dot Notation (Filtering With Sub-resources)

There are times when it makes sense to query a top level resource by a value in one of the sub resources. For example, if I want a list of persons whose home address is in a certain zip code. Resources can support this type of query by implementing dot (`.`) notation. Dot notation allows for a filter parameter to specify the relationship between a top level and a sub-resource property. In order to implement the above example the query string would look something like `https://api.byu.edu/byuapi/persons?addresses.address_type=HOM&addresses.zip_code=84604`  would return a collection of persons with a home address in the `84604` zip code.

#### 6.4 Wildcards

Individual resources can choose to support a wildcard in their query string parameters where they make sense. The asterisk (`*`) should be used as the wildcard character for consistency across resources.

#### 6.5 Comparison Operators

Filters can optionally support operators. The format of a filter including an comparison operator is `filter_name[operator]=value(s)`. The following comparison operators are supported. 

|Operator|Notes|Example
|-----|-----|----
|none(default)|Exact match is required. If the filter supports multiple comma separated values the values are "OR"ed together.|`https://api.byu.edu/byuapi/persons?net_id=johndoe`
|`starts_with`|The property starts with the provided parameter|`https://api.byu.edu/byuapi/persons?net_id[starts_with]=john`
|`ends_with`|The property values end with the provided parameter|`https://api.byu.edu/byuapi/persons?net_id[ends_with]=doe`
|`contains`|The property values contain the provided parameter|`https://api.byu.edu/byuapi/persons?net_id[contains]=john`
|`gt`| The property values are greater than the provided parameter|`https://api.byu.edu/byuapi/persons?net_id[gt]=johndoe`
|`gt_or_eq`|The property values are greater than or equal to the provided parameter|`https://api.byu.edu/byuapi/persons?net_id[gt_or_eq]=johndoe`
|`lt`| The property values are less than the provided parameter|`https://api.byu.edu/byuapi/persons?net_id[lt]=johndoe`
|`lt_or_eq`|The property values are less than or equal to the provided parameter|`https://api.byu.edu/byuapi/persons?net_id[lt_or_eq]=johndoe`
|`not_eq`|The property values are not equal to the provided parameter|`https://api.byu.edu/byuapi/persons?net_id[not_eq]=johndoe`
|`is_null`|Returns `true/false` depending upon if the property value is `null`|`https://api.byu.edu/byuapi/persons?net_id[is_null]=true`
|`is_empty`|Returns `true`/`false` depending upon if the property value is empty|`https://api.byu.edu/byuapi/persons?net_id[is_empty]=true`
|`not_in`|The property values are not in the list contained in the provided parameter. Only applicable for filters that allow for multiple values|`https://api.byu.edu/byuapi/persons?net_ids[not_in]=johndoe,janedoe`


The provider should include in the documentation for the service which operators are allowed for their specific filters.

## 7.0 Search

Filters allow for limiting the contents of a request. An API can optionally support the ability for a consumer to search across a predefined set of properties for resources containing specific text.   

### 7.1 Search Context And Filters

Searching can be done across multiple properties at the same time. APIs group searchable properties into search contexts. Each property in a search context should be logically related. Searches can only be performed against a single search context per request. Searches can be combined with filters to allow for complex operations. The valid combinations are API dependant and should be outlined in the API documentation. 

### 7.2 Search Metadata 

If a resource supports searching it should add the `search_contexts_available` property to its metadata . The value of this property is a JSON object consisting of a JSON array for each of the available search contexts. The JSON array will contain the properties included in that search context in the order they will be searched. 

An example of the search context metadata is as follows: 

```json
        "search_contexts_available": {
            "identifiers": [
                "net_id",
                "sort_name",
                "byu_id"
            ],
            "names": [
                "preferred_last_name",
                "last_name",
                "preferred_first_name",
                "first_name"
            ],
            "locations": [
                "home_country",
                "home_state"
            ]
```

### 7.3 Search Query Parameters

The query parameters used to specify search criteria are as follows: 

|Parameter|Description
|-----|-----
|search\_context|The search context to use. 
|search\_text|The text to search for in the specified context. 

### 7.4 Search Results

Search results are returned as a collection. The `validation_information` metadata property on each instance returned can be used to give information about why the instance was selected. The order of the instances in the collection is determined by the API. Results of the request will be sorted before applying any subset parameters specified in the request (see [3.3.5 Large Collections](#335-large-collections)).

## 8.0 Meta Data Sets

Resources often have associated sets of terms, like accepted state and country names and their abbreviations, that define possible values for properties. These terms, or controlled vocabularies, are necessary for a client to properly use the API. The controlled vocabularies associated with a UAPI compliant resource should be made available through an API published in the the `meta` URL namespace.

### 8.1 Meta Data APIs

Each controlled vocabulary should have a corresponding API with the following characteristics:

- The API should be located under `https://api.byu.edu/byuapi/meta/<top-level-resource>/<vocabulary-name>`.
- Meta APIs should provide HTTP GET support only. Updating values in the controlled vocabulary is left to the domain APIs supporting the data set.
- Meta API responses do not include `metadata` or `links` sections like regular UAPI top level resource responses. 
- Meta APIs provide a single GET verb on the URL `https://api.byu.edu/byuapi/meta/<top-level-resource>/vocabulary-name>` that returns the controlled vocabulary as a JSON array.
- Meta APIs do not support paging.

### 8.2 Domain Meta Data

Controlled vocabularies that are referenced using the `domain` property of the properties metadata ([3.2.3 Properties](#323-properties)) should be structured as follows:
```json
    {
        "value":"<value of this term in the controlled vocabulary>",
        "description":"<short description of this term>",
        "long_description": "<long description of this term>"
    }
```

- All three properties are required and should be not be empty. 
- Values for `description` and `long_description` can be derived from other properties of the data set to provide a standard way to represent each entry in the data set. 
- `description` should be limited to 30 characters in length. It is intended for uses like populating a drop-down list.
- `long_description` should be limited to 256 characters in length. 

#### 8.2.1 Examples of Domain Meta Data

An HTTP GET with the `application/json` mime type on the `state_codes` data set located at the URL `https://api.byu.edu/byuapi/meta/students/state_codes` would return the following:
    
```json
    {
        "values": [
            {
                "value": "ID" ,
                "description": "Idaho",
                "long-description":"Idaho"
            },
            {
                "value": "NV" ,
                "description": "Nevada",
                "long-description":"Nevada"
            },
            {
                "value": "UT" ,
                "description": "Utah",
                "long-description":"Utah"
            },
            {
                "value": "VT" ,
                "description": "Vermont",
                "long-description":"Vermont"
            },
            ...
        ]
    }
```
    
### 8.3 Authorization

Meta Data Sets are considered public. No authorization rules other than any API Manager subscription requirement should be enforced on these APIs. 

### 8.3 Errors

Errors encountered while accessing any Meta URL data set should simply return the appropriate HTTP status code. UAPI error processing as outlined in [12.0 Errors](#120-errors) is not required.

## 9.0 Files 

Files includes images, PDFs, text files, JSON files, etc. Files appear as sub-resources in the URL space of the UAPI but are treated differently. 

### 9.1 File Downloads

Supporting downloads of these files via the UAPI is done through standard REST practices. All files should be directly accessible in a URL space that is part of the UAPI URL space and protected by appropriate measures including requiring an HTTP `Authorization` header. Requests to the URL for a file with no HTTP `Accept` header or the mime-type of the file should return the file in standard HTTP fashion. 

#### 9.1.1 File Metadata

Many files have data associated with them such as creation-date, owner, etc. Requests to the URL for a file with the HTTP `Accept` header containing the mime-type `application/vnd.byu.uapi+json` will return data associated with the file in standard UAPI format. The mime-type `application\vnd.byu.uapi+json` is used to avoid the instance where the file itself is a JSON file. 

### 9.2 File Uploads

Uploading of files is left to the discretion of the API designer but should follow standard industry practices. 

#### 9.2.1 Updating File Metadata

Updating file metadata should be done using standard UAPI POST, PUT, and DELETE methods (see [10.0 HTTP POST, PUT, DELETE](#10-http-post-put-delete) with the mime-type of the request being `application/vnd.byu.uapi+json`. 

## 10.0 HTTP POST, PUT, DELETE

The UAPI spec defines the HTTP PUT and POST verbs (respectively) for modifying and creating new top level and sub-resources. The resource body used in the request is field\_set specific. Generally it is a JSON structure containing all properties as name/value pairs that can be modified for that field\_set.

### 10.1 PUT

The HTTP PUT verb is used to modify a resource. PUTs against the top level resource will update the `basic` field\_set. All other PUTs will be to the URLs of sub-resources associated with the field\_set. 

For example, to update the `basic` field\_set of the persons resource a PUT is sent to the URL `https://api.byu.edu/byuapi/persons/123456789` with the request body as follows: 

```json
{
    "first_name": "Abernathy",
    "high_school_code": "050714",
    "home_country_code": "USA",
    "home_state_code": "UT",
    "home_town": "Provo",
    "middle_name": "Cosmo",
    "preferred_first_name": "Cosmo",
    "preferred_surname": "Cougar",
    "restricted": false,
    "sex": "M",
    "suffix": "II",
    "surname": "Cougar-Rodriguez"
}                            
```

To update the work mailing address of a person resource a PUT is sent to the URL `https://api.byu.edu/byuapi/persons/123456789/addresses/WRK` with the request body as follows:

```json
{
    "address_line_1": "1234 Milky Way",
    "address_line_2": "Highland, UT 84003",
    "address_line_3": " ",
    "address_line_4": " ",
    "building": " ",
    "city": "Highland",
    "country_code": "USA",
    "postal_code": "84003",
    "room": " ",
    "state_code": "UT",
    "unlisted": false,
    "verified_flag": true
}          
```
When the update is completed successfully an HTTP Status Code of `200` should be returned. The body of the response should contain either the field\_sets that were updated or the sub-resource that was updated. 

#### 10.1.1 Resource Creation Using PUT

There are occasions where PUT can be used to create a resource (usually a sub-resource). These occur when the identifier for the sub-resource can be determined before the sub-resource is created. For example, when there exists a defined set of possible identifier values such as the `address_type` of an address resource. The API may assume a PUT to the sub-resource will create an address of the specified type if one of that type doesn't already exist. So a PUT to the URL `https://api.byu.edu/byuapi/persons/123456789/addresses/WRK` would use the values in the body to update the address resource if one exists, otherwise it would create a new resource with that `address_type`.  

Caution should be taken to ensure the logic required to determine the identifier is as minimal as possible to prevent the consumer from having to understand the logic behind identifier creation. 

When a new resource is created an HTTP Status Code of `201` should be returned along with the `Location` HTTP header containing the URL for the new resource. The body of the response should contain either the `basic` field\_set or the sub-resource just created.

### 10.2 POST

The HTTP POST verb is used to insert a new resource or sub-resource. Due to the complexities of creating a new resource the UAPI spec does not specify a format or restrictions on the request body. When a new resource is created an HTTP Status Code of `201` should be returned along with the `Location` HTTP header containing the URL for the new resource. The body of the response should contain either the `basic` field\_set or the sub-resource just created.  

### 10.3 DELETE

The UAPI spec also defines the HTTP DELETE verb for deleting top level and sub-resources. If the DELETE is successful an HTTP Status Code of `204 No Content` should be returned. No response body should be returned. 

### 10.4 Response Representation 

Upon successful completion of a PUT or POST the return representation should be the resource that was updated in the standard UAPI format. 

If errors in the request prevent the POST or PUT from happening the appropriate HTTP Status Code should be returned along with a minimal UAPI standard result representation. Specifically, the `metadata` properties `validation_response` and `validation_information` should be used to convey additional information to the client about the errors that occurred. Notice `validation_information` is an array in order for information about all errors that occurred when processing the request can be returned to the client. 

For example, the following response could be returned when there are multiple validation errors.

```json
"metadata": {
    "restricted": false,
    "validation_response": {
            "code": 400,
            "message": "Bad Request"
            },
    "validation_information": [
         "Invalid home_country_code",
         "Invalid home_state_code",
         "first_name required"
         ]
    },
```

 For more information about handling errors see [12.0 Errors](#120-errors)


## 11.0 Authorization

Authorization for access to UAPI resources falls back to the authorization schemes of individual domains. The UAPI specification only defines what level of authorization is allowed and how denied authorization is communicated to the consumer. 

### 11.1 Field\_set Authorization 

Authorization in the UAPI is done at the field\_set/sub-resource level. Authorization at the property level is not allowed. Properties should be grouped into field\_sets that share common authorization business requirements along with matching the model of the resource as closely as possible. 

#### 11.1.1 Data Classification 

Authorizing by field\_set introduces the dependency that the data classification (see [data classification definitions](https://infohub.byu.edu/resources/classification/summary)) for the entire field\_set is the data classification for the most restricted property it contains. The same applies for complex objects that are included as part of a field\_set. 

### 11.2 Top Level Resource Authorization

In order for a consumer to have access to any part of a top level resource or sub-resources of the top level resource the consumer must at least have access to the `basic` field\_set. Access to other field\_sets/sub-resources is optional. 

### 11.3 Property Authorization

Authorization of access to properties is controlled at the field\_set level. Access to a field\_set implies that at least `read-only` access is allowed for all properties contained in the field\_set. A field\_set may contain a mix of `read-only` and `modifiable` properties. 

### 11.4 Filter Authorization

When selecting properties to offer as filters on resources care needs to be taken to ensure that carefully constructed filtering of resources doesn't allow for inferring data the consumer is not authorized to have. Requests that include filter properties on the query string should be checked for proper authorization to the properties being requested. In general the consumer must have access to at least one field\_set that includes the requested property in order to be authorized to filter on that property. 

If a filter request fails the authorization check the request should be rejected and a `403 Unauthorized` should be returned to the consumer. 

### 11.5 Authorization Failures

Authorization failures should be communicated via standard HTTP Status Codes, typically using the `403 Unauthorized` status code. 

#### 11.5.1 Top Level Resource Authorization Failure

If the consumer does not have authorization to access any part of a top level resource the appropriate status code should be returned for the entire request. The body of the response should contain at least the `metadata` section with the appropriate `validation_response` and `validation_information` properties populated. 

#### 11.5.2 Sub-resource Authorization Failure

If the consumer requests direct access (via the URL) to a sub-resource that is not authorized the response code and response body should be the same as for a forbidden top-level resource request. 

#### 11.5.3 Partial Authorization Failure  

If the consumer requests access to multiple sub-resources via `field_sets` or `context` query parameters the response should be as follows:
- If the consumer does not have access to the top level resource the response should be as outlined in [11.5.1](#1151-top-level-resource-authorization-failure).
- If the consumer does have access to the top level resource the response code for the request should reflect the status of access to the top level resource (e.g. `200` if the resource is accessible, `404` if not found, etc.). Each field\_set property in the response body should reflect the status of access to that field\_set by setting the `validation_response` property of the `metadata` section of the field\_set. Requested field\_sets should always be included in the response body of the request even if they are unauthorized. Unauthorized field\_sets will only have their `metadata` properties set in the response body. 

### 11.6 Restricted Resources
 
 Persons related to the university can request that their records be restricted. A person that has their restricted flag set essentially does not exist for any system that is not authorized to see restricted persons. All resources that deal with any aspect of an individual must implement the restricted resources specification requirements. Resources that do not deal with individuals may include the `metadata` element with the value always set to `false`. 

 #### 11.6.1 Resource Metadata

The `metadata` property for all top level and sub-resources must have the `restricted` element. That element should always be set to the value of the restricted flag for the person (`true`/`false`). The property should appear for all resources even if the consumer does not have access to restricted resources.   

Because collections of top level resources represent multiple resources they do not include the `restricted` element. Each resource within the collection will have the `restricted` element for that resource. Collections of sub-resources do have the `restricted` element because they represent an aspect of the top-level resource which may represent a restricted individual. 

#### 11.6.2 Authorized Access

Access to restricted records by consumers with restricted record authorization should be no different than accessing any other resource provided by the API. The `restricted` element of the `metadata` property should be set to `true` to indicate to the consumer that the resource they are accessing is restricted. Consumers are then obligated to follow established guidelines regarding handling of restricted resources. 

#### 11.6.3 Unauthorized Access Attempts

An attempt to access a restricted top level or sub-resource by an unauthorized consumer should result in exactly the same result as the API would give if the resource did not exist. Typically the HTTP status code of `404 - Not Found` would be returned. 

## 12.0 Errors 

Each resource and collection of resources in the JSON response body has a `metadata` property. The sub-properties `validation_response` and `validation_information` are used to convey to the consumer information about the state of the resource or resource collection. 

### 12.1 HTTP Status Codes

Standard [HTTP status codes](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) should be used for the overall status of the request and for each sub-resource request included in the request. 

### 12.2 Error Response Format
 
The response body should be formatted in standard UAPI format with only the `metadata` section being required. The `metadata` section should include the `validation_response` with the HTTP status code and description. The optional `validation_information` metadata property can also be included to provide additional information to the consumer about the reasons for the request failure. 

#### 12.2.1 validation_response

The `validation_response` metadata property should contain the HTTP status code and description for the operation requested on the resource in the following format: 

```json
    "validation_response": {
        "code": 200,
        "message": "Success"
    }
```

#### 12.2.2 validation_information

The optional `validation_information` metadata property can be used to communicate multiple status messages about the operation requested on the resource. It is a JSON array in the following format:

```json
    "validation_information": [
         "Invalid home_country_code",
         "Invalid home_state_code",
         "first_name required"
         ]
```

### 12.3 Top Level Resource Errors

Requests for top level resources take three forms - requests for collections of resources (no identifier on the resource URL) and requests for specific resources (identifier included in the URL) and either of the two with `field_sets` or `contexts` query string parameters.  

#### 12.3.1 Single Top Level Resource Errors

Errors in processing a request for a single top level resource with no additional field\_sets requested should be returned as the HTTP status code for the request as well as the `basic` field\_set representation containing at least the `metadata` property with the `validation_response` and optionally the `validation_information` property. 

#### 12.3.2 Top Level Resource Collection Errors

Errors in processing a request for a collection of top level resources should proceed as follows:

- If an error occurs with the overall request the HTTP status code for the request should indicate the type of error that occurred. The body of the response should contain a UAPI standard format response containing at least the `metadata` property with the `validation_response` property and optionally the `validation_information` property. 
- If an error occurs in processing the request for one or more of the resources in the collection the `validation_response` and `validation_information` metadata properties for that resource should be set with the appropriate error information.  The HTTP status code for the entire request may be a `200 Success` even if only part of the response is error free. 

### 12.4 Sub-resource Errors

Errors during access to individual sub-resources with or without identifiers should be handled the same way as top level resources outlined in [12.3 Top Level Resource Errors](#123-top-level-resource-errors). 
 
### 12.5 Partial Error Response
 
 A single request to a top level resource requesting multiple sub-resources via field\_sets or contexts presents the possibility that some sub-resource access may fail while others will succeed. To reflect this situation the following rules apply:
 - The HTTP status code for the entire request is `200 Success`. 
 - The field\_set properties for parts of the response that experienced errors will contain at least the `metadata` property with the appropriate values for the `validation_response`, `validation_information`, and `restricted` properties. 
 - The field\_set properties for parts of the response that succeeded will contain the UAPI representation of the field\_set with the `validation_response` set to the appropriate HTTP status code, normally `200 Success`. 
 
### 12.6 Special Error Situations

#### 12.6.1 '404 Not Found' Errors

Because of the need to protect restricted resources (see [11.6 Restricted Resources](#116-restricted-resources)), when a `404 Not Found` is to be returned special rules apply:

- If the request is for a single top-level resource or sub-resource addressed directly via the URL the `404 Not Found` HTTP status code should be returned with no body attached to the result. 
- If the request is for a top-level resource (single or collection) that includes multiple sub-resources via field\_sets or contexts and one or more of the sub-resources is not found the rules for Partial Error Response [12.5 Partial Error Response](#125-partial-error-response) apply. 

#### 12.6.2 Invalid Query Parameters

When a request contains an undefined query parameter or a query parameter that contains an invalid value the HTTP status code `400 Bad Request` should be returned and the body of the message should contain a valid `validation_response`. In order to aid the consumer with correcting the problem the API should include the `validation_information` metadata property with messages that specify which query parameter(s) was problematic and why. 


------


## Version History
|Version|Date|Changes|
|-----|-----|-----|
|1.0||Original published standard|
|1.1||Complete rewrite of the standard document to include new items such as field\_sets, contexts, etc. All information about domain APIs has been moved to a separate document.|
|1.2|May 31, 2018|Added date/time specification, clarified top level resource links and metadata, fixed dot notation example. 
|1.3|July 19, 2018|Added requirements around restricted persons including clarifying how 404 errors are to be handled. Added 403 error for invalid field\_set and context parameters. Added sections for sorting, subsets of large collections, and search.  Other minor enhancements and clarifications. 
|1.4|?|Added support for complex objects and specify which data types are acceptable.
|1.5|?|Added support for conditional operators on filters.

