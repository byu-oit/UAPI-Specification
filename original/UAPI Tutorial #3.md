## University API Tutorial - Example #3

The purpose of the third example is to:

*   Show how we include information about each data attribute of the answer
*   Demonstrate example #3 in the WSO2 API manager
*   Present the third example as a Swagger 2.0 document
*   Show how to use the enriched data attributes to understand the resource
*   Show how to use the enriched data attributes to develop your custom application
*   Explain the relationship between data attributes and authorizations

### Enriching the Data Attribute

Obviously, when a client application invokes a GET method on a web service, it wants the data represented by the resource. At the same time, example #2 showed how including metadata and HATEOAS links along with the data allows the client application to improve the user experience which makes the data even more valuable. There is also information about the data attributes themselves that could have the same effect. Let's start by examining the data returned in example #2.

```
{
  "links": {  Links related to the collection of cars },
  "metadata": { Metadata about the collection of cars },
  "values": [
    { "basic": {
        "links": { Links related to this car },
        "vin": "1FTBE14A4NP05962",
        "plate_state": "UT",
        "plate_number": "Q580RT",
        "vehicle_type": "Truck/SUV",
        "vehicle_year": "2013",
        "make": "Toyota",
        "model": "RAV4",
        "color": "Dark Green",
        "owner_id": "783783783",
        "owner_name": "Daisy Duck",
        "created_by_id": "586586586",
        "created_by_name": "Daffy Duck",
        "created_date_time": "2014-05-11T13:45:53.09Z",
        "updated_by_id": "783783783",
        "updated_by_name": "Daisy Duck",
        "updated_date_time": "2014-09-15T08:12:44.03Z"
      }
    },
    { Other items in the collection }
  ]
}
```

Clearly, example #2 was only returning the value for the data attribute. In example #3, we will explain how the University API will also send metadata about the data attribute as well as the value. Let's look specifically at the `plate_state` data attribute. In example #2, we sent:

```
"plate_state": "UT"
```

The University API will enrich this answer by sending the following

```
"plate_state": {
  "value": "UT",
  "api_type": "modifiable",
  "description": "Utah",
  "display_label": "Plate State",
  "domain": "https://api.byu.edu/byuapi/meta/plate_state"
}
```

The `plate_state` data attribute is changed to an object that contains the data in the `value` tag, but it also contains other information useful to the client application. The tags used by the University API are described in the following table:

|Tag Name|Description|
|-----|----
|value|Contains the data.
|api_type|Describes how this data attribute may be used in this API. For example, may it be modified. The next section describes the possible values for api_type and the rules associated with each value.
|related_resource|If the API type is `related`, this tag will contain the resource-name that "owns" this data attribute. Usually, the resource-name can be used to find a HATEOAS link (the `rel` will be `resource-name__info`) that can access this data attribute.
|description|If the data value is a code (less than human-friendly), the translated value for display purposes is packaged with the attribute.
|long_description|Some code values also have a longer description translation.
|display_label|Provides a string to use when creating a label for this property in the user interface if the name of the property is not appropriate (or is too cryptic) to use as a user interface label.
|domain|Especially with code values, there is a limited set of allowable codes. This tag contains the URL that can be used to retrieve the set of allowable codes.

Rarely, will a single data attribute use all of the available tags, but value and `api_type` will always be included. The possible values of `api_type` are:

|api_type|may be modified?|Description
|-----|------|-----
|key|Depends on the API|The data attribute is one of the key elements for this resource. This has several implications. First, key fields are required fields - they are not allowed to be blank or null. Second, it is used in the URL segment of the URL to access a specific item in the resource collection. Some resources may not allow a key value to be modified, but they are the exceptions. Refer to the documentation for the API. Some resources may assign the key value when the item is added. Once again, refer to the documentation for the API.
|read-only|No|There are instances when this data attribute may be modified, but there are either business rules or authorization considerations that do not allow the data attribute to be changed at this time by this client.
|modifiable|Yes|The data attribute may be modified.
|derived|No|This is an attribute that was derived by a "calculation" on other data attributes. An example might be GPA. An API will not support the modification of derived data. In our example, to modify a GPA, you would have to modify grades or credit hours on individual classes.
|related|No|This means that the data attribute was included in this API to make the API more useful, but it doesn't really belong to this API and the business logic to modify the data attribute exists in the `related_resource`. In our example, `owner_name` is a `related` data attribute. We included the `owner_name` in the Cars API because our business analysis showed that it is always wanted when clients retrieve a list of cars. (`owner_id` isn't that useful.) But, a single owner may own dozens of cars and it would not be appropriate to change the `owner_name` on a single car. Instead the client application is referred to the `owners` API.
|system|No|The data value was assigned by the system. If it can be modified, it will be by the system and not through API input. A date-time field that tracks the last time the resource was updated is a good example of a system data attribute.

With this much of an explanation, let's rework our example:

```
{
  "links": {  Links related to the collection of cars },
  "metadata": { Metadata about the collection of cars },
  "values": [
    { "basic": {
        "links": { Links related to this car },
        "vin": {
          "value": "1FTBE14A4NP05962",
          "api_type": "key"
        },
        "plate_state": {
          "value": "UT",
          "api_type": "modifiable",
          "description": "Utah",
          "domain": "https://api.byu.edu/domains/tutorials/meta/plate_state"
        },
        "plate_number": {
          "value": "Q580RT",
          "api_type": "modifiable"
        },
        "vehicle_type": {
          "value": "Truck/SUV",
          "api_type": "derived",
          "domain": "https://api.byu.edu/domains/tutorials/meta/vehicle-type"
        },
        "vehicle_year": {
          "value": "2013",
          "api_type": "modifiable"
        },
        "make": {
          "value": "Toyota",
          "api_type": "modifiable",
          "domain": "https://api.byu.edu/domains/tutorials/meta/cars/make"
        },
        "model": {
          "value": "RAV4",
          "api_type": "modifiable",
          "domain": "https://api.byu.edu/domains/tutorials/meta/cars/model/Toyota"
        },
        "color": {
          "value": "Dark Green",
          "api_type": "modifiable",
          "domain": "https://api.byu.edu/domains/tutorials/meta/cars/color"
        },
        "owner_id": {
          "value": "783783783",
          "api_type": "modifiable"
        },
        "owner_name": {
          "value": "Daisy Duck",
          "api_type": "related",
          "related_resource": "owners"
        },
        "created_by_id": {
          "value": "586586586",
          "api_type": "system",
          "description": "Daffy Duck"
        },
        "created_date_time": {
          "value": "2014-05-11T13:45:53.09Z",
          "api_type": "system"
        },
        "updated_by_id": {
          "value": "783783783",
          "api_type": "system",
          "description": "Daisy Duck"
        },
        "updated_date_time": {
          "value": "2014-09-15T08:12:44.03Z",
          "api_type": "system"
        }
      }
    },
    { Other items in the collection }
  ]
}
```

A couple of things stand out in this example.

First, the `/domains/tutorials/meta` API is an extremely important API. It is where we store code sets. The domain tag will usually refer to this API.

Second, this organization allows the API designer to combine data attributes that are really codes and their descriptions.

**Combining Data Attributes - Example #1**

```
"country_code": "USA",
"country_name": "United States",
"official_country_name": "United States of America"
```

   could be represented as:

```
"country_code": {
   "value": "USA",
   "description": "United States",
   "long_description": "United States of America"
}
```

This technique can also be used for data attributes that aren't pure "code" data attributes. For example, if a "foreign key" for another resource appears in an API, it may be useful to include descriptive information since the key attribute is probably not very human-friendly.

**Combining Data Attributes - Example #2**

```
"owner_id": "783783783",
"owner_name": "Daisy Duck"
```

could be represented as:

```
"owner_id": {
    "value": "783783783",
    "description": "Daisy Duck"
}
```

### Swagger Documents for Example #3

Example #3 may be explored in the WSO2 API Manager as the Cars v3 API.

To access the Swagger 2.0 specification for Cars v3: [https://github.com/byu-oit-appdev/api-repo-sandbox/blob/master/swagger20/apitutor3/apitutor3.swag20](https://github.com/byu-oit-appdev/api-repo-sandbox/blob/master/swagger20/apitutor3/apitutor3.swag20)

The only significant changes in the Swagger documents is in the model/definition section. Please note that the Swagger documents can specify which tags make sense with each data attribute and what are the allowable api_types.

### Special Discussion: How can the Client Application Leverage the Data Attribute Information?

Much like the HATEOAS links, the additional information about the data attribute improves the ability of the API consumer to discover the capabilities of the API. But, once again, this is a minor benefit.

The University API will populate the `api_type` based on the current business rules and the authorizations of the client invoking the API. This means that a data attribute that is `modifiable` at some point in the calendar may be `read-only` at other times or for a different client. Placing all of this logic in the client application is not a maintainable solution. The server-side API code will make these determinations and communicate them to the client application. The client application can then shape the user experience by graying-out data attributes that are not modifiable. Even data attributes that may be `derived` in some instances may be `modifiable` in other instances. The `vehicle_type` attribute in our example APIs illustrates this point. If the `make` and `model` are populated, they determine the `vehicle_type` and it becomes a derived attribute. However, if the `model` is not populated (null value), then the `vehicle_type` can not be derived and it may be modified through API input.

Clearly, data attributes that are `related` in one API would be `modifiable` in the `related_resource` API. However, the user experience for a `related` data attribute could be shaped to be more powerful. Perhaps the UI designer could place a button next to the `related` data attribute that opens to an interface that displays (and allows modification to) the `related_resource`.

The `domain` tag could be used to populate drop-down lists or perhaps perform an AJAX-style verification of a code value real-time. A minor benefit to the server-side API developer is that it is possible to change the URL where a domain is retrieved without impacting client code.

Proper use of the `api_type` tag could also help the client application correctly populate PUT requests depending on shifting business rules and authorizations.

[Previous Example](/docs/consume-api/use-api/university-api-tutorial/university-api-tutorial-example-2)   [Next Example](/docs/consume-api/use-api/university-api-tutorial/university-api-tutorial-example-4)
