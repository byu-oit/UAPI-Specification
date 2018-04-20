## University API Tutorial - Example #4

The purpose of the fourth example is to:

*   Explain why supplementing a resource is more useful and easier to understand
*   Explain how both the URL formats and the data structure are supplemented together
*   Demonstrate example #4 in the WSO2 API manager
*   Present the fourth example as a Swagger 2.0 document
*   Clarify the little details (filtering, URL segments, etc.)
*   How do we provide additional documentation

### Supplementing the Cars Resource

Through the first three examples, we defined an API (URL and JSON data structure) that enhance both discovery and usability. We could take this approach and create hundreds of resources for our "Fleet Management" business. Let's try it out.

We periodically make odometer readings on all of the vehicles. For each vehicle, we record the following information:

|attribute|description
|-----|-----
|vin|This is the key to the vehicle resource. We use this to know which vehicle's odometer was read.
|read\_date|The date that we read the odometer.
|miles|The value on the odometer.
|read\_by\_id|The id of the individual that read the odometer.
|read\_by\_name|The name of the individual that read the odometer.

Maybe the simplified JSON data structure for the `odometer_log` resource could look something like this. (Simplified in this case means that I'll ignore the HATEOAS links and I won't make each data attribute a complex object)

```
{
  "links": { Links related to the collection of odometer_logs },
  "metadata": {
    "collection_size": 3,
    "page_start": 1,
    "page_end": 3,
    "page_size": 50,
    "default_page_size": 50,
    "max_page_size": 1000
  },
  "values": [
    { "basic": {
        "links": { Links related to this odometer_log entry },
        "vin": "1FTBE14A4NP05962",
        "read_date": "2014-05-11",
        "miles": 32459,
        "read_by_id": "586586586",
        "read_by_name": "Daffy Duck"
      }
    },
    { "basic": {
        "links": { Links related to this odometer_log entry },
        "vin": "1FTBE14A4NP05962",
        "read_date": "2014-06-08",
        "miles": 33782,
        "read_by_id": "586586586",
        "read_by_name": "Daffy Duck"
      }
    },
    { "basic": {
        "links": { Links related to this odometer_log entry },
        "vin": "1FTBE14A4NP05962",
        "read_date": "2014-07-12",
        "miles": 35121,
        "read_by_id": "783783783",
        "read_by_name": "Daisy Duck"
      }
    }
  ]
}
```

And maybe the URL structure could look something like this:

|http_method|URL|description
|-----|-----|-----
|GET|http://api.byu.edu/domains/tutorials/odometer\_log|Retrieve the collection of odometer readings. It would make sense if the collection could be filtered by vin, a range of read\_dates, or possibly by read\_by\_id.
|POST|http://api.byu.edu/domains/tutorials/odometer\_log|Posts a new reading to the odometer log.
|OPTIONS|http://api.byu.edu/domains/tutorials/odometer\_log|What HTTP methods are supported by this endpoint?
|GET|http://api.byu.edu/domains/tutorials/odometer\_log/{vin},{read\_date}|Retrieve a specific odometer reading.
|PUT|http://api.byu.edu/domains/tutorials/odometer\_log/{vin},{read\_date}|Correct an odometer reading. Of course, this would be a highly sensitive function since it could undermine the integrity of the system.
|DELETE|http://api.byu.edu/domains/tutorials/odometer\_log/{vin},{read\_date}|Delete an odometer reading. Once again, this would be a very sensitive function.
|OPTIONS|http://api.byu.edu/domains/tutorials/odometer\_log/{vin},{read\_date}|What HTTP methods are supported by this endpoint?

Pretty simple. All we have to do now is identify business functions, add HATEOAS links, add richness to the data attributes, and figure out the authorization rules. We can stamp out hundreds of APIs pretty dang fast!

And maybe that is the problem. Do we really want hundreds of APIs? There are three problems with this approach.

First, it makes discovery so much more complex. The client developer has to sort through hundreds of APIs to find the information that they want and has to understand how the APIs are related together. Quite often, this is not immediately obvious. As an example, I would offer the university's class schedule. We don't keep the location and times taught for a class in the classes resource. We keep this information in an events resource. A class is just another event that reserves a room at a specific time (possibly repeating). We keep all of our events in the same resource so that classes and other non-academic events can share locations without colliding. Even less obvious is the key to the events resource and how it relates to the classes resource.

Second, hundreds of narrowly defined APIs may not make sense with our business functions. If our most common business functions always require the client to access dozens of APIs we're probably doing something wrong. We've placed too much logic in every client application. In the case of classes and events, we clearly want the time and location of the class in most of our business functions (and all of our highly used business functions). It should be returned as part of the classes resource. It should probably also be returned as part of the events resource since it is also needed there. The fact that information is returned in two resources does not dictate anything about the underlying data model and business modules. In fact, the API should transform the underlying data models to make them more useful for the business functions. Obviously, **designing a useful API requires an understanding of the business functions and their use cases**. It is not an exercise in data normalization!

Third, if I don't include the time and location of a class in the classes resource, my API may perform so poorly that it is not usable. For instance, I may access the classes resource to retrieve a collection of all MATH classes taught in Fall Semester. (This is actually a very common use case. It is used by students looking for a class to add.) Let's suppose there are several hundred sections of MATH offered. If I have to turn around and make a separate web service invocation for each class in the collection to retrieve the time and location of the class (yes, students do care about this when they are adding classes), performance may be so bad that I can't retrieve the answer before the student's browser times out. It would be equivalent to suggesting that I can't perform joins with a relational database. I would be forced to perform the joins in code which would cause the code to be unnecessarily bloated, complicated, and slow. In short, **optimize the API for your important business functions**.

Perhaps the `odometer_log` is not a resource for which we want to create a separate API. An analysis of our business functions tells us that we only need the `odometer_log` when we are looking at a vehicle. Let's simply include the `odometer_log` as a sub-resource of the Cars API. When you retrieve a collection of cars, you get the `odometer_logs` with them. Perhaps a simplified JSON structure for the cars resource that includes the `odometer_log` as a sub-resource would look like this:

```
{
  "links": {  Links related to the collection of cars },
  "metadata": { Metadata about the collection of cars },
  "values": [
    { 
      "basic": {
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
        },
      "odometer_logs: [
        { "vin": "1FTBE14A4NP05962",
          "read_date": "2014-05-11",
          "miles": 32459,
          "read_by_id": "586586586",
          "read_by_name": "Daffy Duck"
        },
        { "vin": "1FTBE14A4NP05962",
          "read_date": "2014-06-08",
          "miles": 33782,
          "read_by_id": "586586586",
          "read_by_name": "Daffy Duck"
        },
        { "vin": "1FTBE14A4NP05962",
          "read_date": "2014-07-12",
          "miles": 35121,
          "read_by_id": "783783783", 
          "read_by_name": "Daisy Duck"
        }
      ]
    },
    { Other items in the collection of cars }
  ]
} 
```

### Understanding Sub-Resources

Let's see if we can get a bit more definition around sub-resources. As shown in our example, it is important to understand when to convert a resource into a sub-resource in your API design. Let's test `odometer_logs` against some questions.

First question: Does the `odometer_logs` resource stand on it's own?

Would we have an instance of the `cars` resource even if there are no instances of the `odometer_logs` for this vehicle? The answer would be "yes". Let's try it the other way. Would there be an instance of the `odometer_logs` even if there was no instance of the `cars` resource? In our business cases, the answer would be "no". Obviously, an understanding of the key data attributes for each of these resources help answer this question, but they can sometimes be misleading. It also requires an understanding of how our business functions. Based on these answers, the `odometer_logs` resource is a candidate to be a sub-resource of the `cars` resource.

Second question: Do our business functions access `odometer_logs` only in the context of the `cars` resource?

If the only time that our business functions ("important and frequently used" should be a criteria) access `odometer_logs` is when they are accessing the vehicle through the `cars` resource, then we should package them together for the client. Let's not force all the client applications to "join" the `cars` and `odometer_logs` resources in code. If the only time we would look at `odometer_logs` outside of the context of a vehicle is for complex analysis, perhaps this business case would be better satisfied with a Business Intelligence (BI) solution than through the API.

Third question: Do we perform a large number of complex business functions on the `odometer_logs` resource?

If many of our important and complex business functions are operations against the `odometer_logs` resource, perhaps it should remain a resource (a separate API). This would emphasize its importance and help the developer who is attempting to discover and understand the API offering. This goes back to understanding our business functions and points out the "art" of API design. Dr. Windley wrote an excellent [blog article](http://www.windley.com/archives/2014/12/the_core_of_your_api.shtml) on this subject.

Fourth question: Does leaving `odometer_logs` as a separate resource (and thus API) make it easier for a developer to understand/discover the University API offering or does it add unhelpful noise and complexity?

We believe that it will be easier for a developer to understand and discover the University API offering if there are dozens of top-level resources (APIs) instead of hundreds. It is a guiding principle on our design decisions.

Since `odometer_logs` don't make sense on their own, developers (and our business functions) generally think of `odometer_logs` in the context of the `cars` resource, and we perform very few business functions (and none of them are critical or complex) against `odometer_logs`, we should make `odometer_logs` a sub-resource of the cars resource.

### A Few More Sub-Resources for our Example

To illustrate how the University API accesses sub-resources, let's add a few more sub-resources to our cars resource.

First, we want to record all of the major trips in which a vehicle was used. We'll call this the `trips` sub-resource. Second, we want to keep track of the current insurance policy for the vehicle. There is only one, but to remain consistent (all resources and sub-resources are plural) we'll call it `insurance_policies`. Third, we want to keep track of maintenance activities on our vehicles. We'll call this `maintenance_logs`. A simplified JSON structure for cars and all the sub-resources would look like this:

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
      },
      "odometer_logs: [
        { "vin": "1FTBE14A4NP05962",
          "read_date": "2014-05-11",
          "miles": 32459,
          "read_by_id": "586586586",
          "read_by_name": "Daffy Duck"
        },
        { Other items in the collection of odometer_logs }
      ],
      "trips": [
        { 
          "vin": "1DTGR14A6NP38861",
          "trip_id": "A326",
          "destination": "Butte, Montana",
          "start_date": "2012-09-06",
          "end_date": "2012-09-10",
          "start_miles": 8265,
          "end_miles": 9854
        },
        { Other items in the collection of trips }
      ],
      "insurance_policies": {
        "vin": "1DTGR14A6NP38861",
        "policy_id": "421T84GX333",
        "insurer": "State Farm"
      },
      "maintenance_logs": [
        { 
          "vin": "1DTGR14A6NP38861",
          "maintenance_id": "000401",
          "maintenance_date": "2013-08-01",
          "description": "New Tires",
          "cost": "533.12"
        },
        { Other items in the collection of maintenance_logs } 
      ]
    },
    { Other items in the collection of cars }
  ]
} 
```

There are a couple of interesting things to note about these examples before we move on. In the next few sections will give an explanation for these modeling decisions.

*   A sub-resource does not have to be a collection. In the case of `insurance_policies`, our API expects that there will only be one insurance policy per vehicle. We could simply include the insurance information in the `basic` object, but we may want to keep it separate to more easily manage access management.
*   In our modeling, we always include the `vin` in every sub-resource, even though we have it in the basic area of the cars resource. The JSON for each sub-resource should stand on its own.

### Addressing Sub-Resources - The URL Mirrors the JSON Structure

Sub-resources have the potential to create quite a bit of code complexity. For example:

*   Do I always have to return the entire sub-resource collection?
*   How do I update a single item of a sub-resource?
*   How do I add another item to a sub-resource?
*   Do I always have to return all of the sub-resources for a Car if I only want one of them?

You get the idea. Since some collections of sub-resources could get quite large, we probably should probably develop some solutions to meet these needs.

First, we allow the client to specify which sub-resources (we also refer to sub-resources as ***field_sets***) they would like to have returned. For example, the URL `http://api.byu.edu/domains/tutorials/cars?field_sets=basic,odometer_logs,trips` would return only the `odometer_logs` and `trips` sub-resources with the collection of cars. `http://api.byu.edu/domains/tutorials/cars?field_sets=basic,insurance_policies&make=FORD` would return a collection of Ford vehicles and their insurance policies. It would include the `basic` field\_set, but not the `odometer_logs`, `trips`, and `maintenance_logs` field\_sets. If no field\_sets query parameter is provided, resources will have a default set of field\_sets that are returned. `field_sets=ALL` will return all available field\_sets for a resource.

Some resources may have quite a long list of sub-resources associated with them. If I commonly have to ask for 6 or 7 field\_sets with a resource, the URLs can get quite long and cumbersome. We allow groups of field\_sets known as ***contexts***. For example, if we commonly ask for `basic`, `odometer_logs`, and `trips` together, we could define a context known as `mileage`. This would mean that `http://api.byu.edu/domains/tutorials/cars?contexts=mileage` is the same thing as `http://api.byu.edu/domains/tutorials/cars?field_sets=basic,odometer_logs,trips`. As many contexts for a resource can be defined as needed. It is possible to ask for multiple contexts just like it is possible to ask for multiple field\_sets. You are allowed to mix contexts and field\_sets. The result is a union of all the fields_sets and contexts. For example, `http://api.byu.edu/domains/tutorials/cars?contexts=mileage&field_sets=trips,insurance_policies` would return the `basic`, `odometer_logs`, `trips`, and `insurance_policies` field\_sets. The only field\_set not returned would be `maintenance_logs`.

Second, we allow the URL to work directly with a sub-resource collection of a specific Car. For example, the URL `http://api.byu.edu/domains/tutorials/cars/1FTBE14A4NP05962/odometer_logs` would return the following simplified JSON structure:

```
[
  {
    "vin": "1FTBE14A4NP05962",
    "read_date": "2014-05-11",
    "miles": 32459,
    "read_by_id": "586586586",
    "read_by_name": "Daffy Duck"
  },
  { 
    "vin": "1FTBE14A4NP05962",
    "read_date": "2014-06-08",
    "miles": 33782,
    "read_by_id": "586586586",
    "read_by_name": "Daffy Duck"
  },
  { 
    "vin": "1FTBE14A4NP05962",
    "read_date": "2014-07-12",
    "miles": 35121,
    "read_by_id": "783783783", 
    "read_by_name": "Daisy Duck"
  }
]
```

Note that it returns the collection of `odometer_logs`, but does not wrap it in the Cars JSON structure. It is the reason that it is important to include the `vin` in JSON structure of the sub-resources. Each JSON structure should stand on its own which allows it to be passed around and manipulated in program code.

Third, we even allow the URL to work directly with a specific item in a sub-resource collection for a specific car. For example, the URL `http://api.byu.edu/domains/tutorials/cars/1FTBE14A4NP05962/odometer_logs/2014-05-11` (this assumes that the key data attributes for the `odometer_logs` sub-resource is `vin+read_date`) would return the following simplified JSON structure:

```
{ 
  "vin": "1FTBE14A4NP05962",
  "read_date": "2014-05-11",
  "miles": 32459,
  "read_by_id": "586586586",
  "read_by_name": "Daffy Duck"  
}
```

In essence, the URL structure mirrors the JSON data structure. There will be a URL segment that corresponds to every field\_set. Using our example, I should expect the following URLs and operations for the supplemented Cars resource (base path = `http://api.byu.edu/domains/tutorials/cars`):

|URL|http_method|description
|-----|-----|-----
|.../|GET|Retrieve a collection (possibly filtered) of cars. It may also include 0 to many field_sets.
|.../|POST|Add a Car resource from the body of the post. If field\_set collections are included in the request\_body, the sub-resources are also added to the database.
|.../{vin}|GET|Retrieve a specific Car resource. It may also include 0 to many field_sets.
|.../{vin}|PUT|Modify a specific Car resource from the request\_body. In this instances, field\_sets are not processed even if they are included in the request_body.
|.../{vin}|DELETE|Delete a specific Car resource. Sub-resources may or may not be deleted.
|.../{vin}/odometer\_logs|GET|Retrieve a collection (possibly filtered) of `odometer_logs` for a specific Car.
|.../{vin}/odometer\_logs|PUT|Add a new `odometer_log` entry from the request\_body.
|.../{vin}/odometer\_logs/{read\_date}|GET|Retrieve a specific `odometer_log` sub-resource.
|.../{vin}/odometer\_logs/{read\_date}|PUT|Modify a specific `odometer_log` sub-resource. (Depending on the controls in the system, it may not be possible to modify an `odometer_log`. If this true, we would not need to define this operation.)
|.../{vin}/odometer\_logs/{read\_date}|DELETE|Delete a specific `odometer_log` sub-resource. (Depending on the controls in the system, it may not be possible to delete an `odometer_log`. If this true, we would not need to define this operation.)
|.../{vin}/trips|GET|Retrieve a collection (possibly filtered) of `trips` for a specific Car.
|.../{vin}/trips|PUT|Add a new `trip` entry from the request_body.
|.../{vin}/trips/{trip_id}|GET|Retrieve a specific `trip` sub-resource.
.../{vin}/trips/{trip_id}|PUT|Modify a specific `trip` sub-resource. (Depending on the controls in the system, it may not be possible to modify a `trip`. If this true, we would not need to define this operation.)
|.../{vin}/trips/{trip_id}|DELETE|Delete a specific `trip` sub-resource. (Depending on the controls in the system, it may not be possible to delete a `trip`. If this true, we would not need to define this operation.)
|.../{vin}/insurance\_policies|GET|Retrieve the insurance policy for this Car. In this case, we only record a single policy for each vehicle. This operation does not return a collection.
|.../{vin}/insurance\_policies|PUT|Add or modify the insurance policy for this Car. Since there is only a single policy for each Car, this operation performs both the add and modify function.
|.../{vin}/insurance\_policies|DELETE|Remove the insurance policy from this Car. Since `insurance_policies` is not a collection, no key attribute(s) is required.
.../{vin}/maintenance\_logs|GET|Retrieve a collection (possibly filtered) of `maintenance_logs` for a specific Car.
|.../{vin}/maintenance\_logs|PUT|Add a new `maintenance_logs` entry from the request\_body.
|.../{vin}/maintenance\_logs/{maintenance\_id}|GET|Retrieve a specific `maintenance_log` sub-resource.
|.../{vin}/maintenance\_logs/{maintenance\_id}|PUT|Modify a specific `maintenance_log` sub-resource. (Depending on the controls in the system, it may not be possible to modify a `maintenance_log`. If this true, we would not need to define this operation.)
|.../{vin}/maintenance\_logs/{maintenance\_id}|DELETE|Delete a specific `maintenance_log` sub-resource. (Depending on the controls in the system, it may not be possible to delete a `maintenance_log`. If this true, we would not need to define this operation.) 

There are several takeaways from this illustration:

*   The API's endpoints and the JSON structure of a resource really do mirror each other. If I am looking at the API's endpoints, I should be able to guess at the basic JSON structure that will be returned. If I am looking at the JSON data, I should be able to guess at the API's endpoints. This should make the API easier to discover and understand.
*   Adding or updating a sub-resource are both PUT operations. There is a continuing, healthy debate whether adding a sub-resource should be a POST or PUT. We've settled on PUT for the following reason. POST loosely means that we are adding a new resource. We've already stated that a sub-resource is "part" of a resource and does not stand on its own. Adding a sub-resource is not like adding a new resource. Adding a sub-resource should be thought of as modifying the resource. For example, we've "added another `odometer_log` entry to vehicle X" which modifies the cars resource. We don't say we've "added another `odometer_log` entry". The very next question would be, "To which vehicle?"
*   Some resources/sub-resources simply should not be modified or deleted. The best example is accounting transactions. You may retrieve the set of transactions on an account or you may add another transaction. You may not modify or delete existing transactions. Log files are another example and will be common in the University API. The HTTP methods available on each endpoint should reflect these business rules.

### Supplementing the Metadata

In Example #2, we introduced the concept of metadata that describes the resource collection that is being returned. The `metadata` block contains the following attributes:

```
"metadata": {
  "collection_size": 3,
  "page_start": 1,
  "page_end": 3,
  "page_size": 50,
  "default_page_size": 50,
  "max_page_size": 1000
}
```

With the addition of sub-resources, we should also consider supplementing the metadata information that we provide with a collection. The following attributes are now suggested with returning a resource collection:

```
"metadata": {
  "collection_size": 3,
  "page_start": 1,
  "page_end": 3,
  "page_size": 50,
  "default_page_size": 50,
  "max_page_size": 1000,
  "field_sets_returned: [
    "basic"
  ],
  "field_sets_available: [
    "basic",
    "odometer_logs",
    "trips",
    "insurance_policies",
    "maintenance_logs"
  ],
  "default_field_sets": [
    "basic"
  ],
  "contexts": [
    "mileage": [
      "basic",
      "odometer_logs",
      "trips"
    ]
  ]
}
```

### Swagger Documents for Example #4

Example #4 may be explored in the WSO2 API Manager as the Cars v4 API.

To access the Swagger 2.0 specification for Cars v4: [https://github.com/byu-oit-appdev/api-repo-sandbox/blob/master/swagger20/apitutor4/apitutor4.swag20](https://github.com/byu-oit-appdev/api-repo-sandbox/blob/master/swagger20/apitutor4/apitutor4.swag20)

### Special Discussion: Filtering a Sub-Resource

Under construction

As previously demonstrated, it is possible to filter a resource collection through the use of query parameters. 

For example, `https://api.byu.edu/domains/tutorials/cars?color=red&make=Ford` would return a JSON array that only contains red Fords. Depending on the implementation of the API, it may also be possible to filter the resource collection by a value in one of the sub-resources. For example, `https://api.byu.edu/domains/tutorials/cars?color=red&make=Ford&trips.destination=Atlanta*` would return the set of red Ford cars that have been used to travel to Atlanta. Note the use of the dot notation to designate the sub-resource. Also, note the use of the "\*" wild card to describe the destination. You would have to refer to documentation on the API to know which sub-resource attributes can be used to limit the resource collection and whether or not the use of wild cards is allowed. 

It should also be noted that the `trips.destination=Atlanta*` only filters the collection of cars returned. It does not filter which trips are returned for each vehicle. For example, `https://api.byu.edu/domains/tutorials/cars?color=red&make=Ford&trips.destination=Atlanta*&field\_sets=basic,trips` would return the set of red Ford cars that have been used to make a trip to Atlanta, but it would return all of the trips that each of the cars have made. 

A good rule of thumb is that query parameters filter the last resource name in the URL path. In the URL `https://api.byu.edu/domains/tutorials/cars?color=red&make=Ford&trips.destination=Atlanta*&field\_sets=basic,trips`, the last resource name in the URL path is `cars`. This means that the query parameters filter the collection of cars. In the URL `https://api.byu.edu/domains/tutorials/cars/1FTBE14A4NP05962/trips?destination=Atlanta*`, the last resource (sub-resource) name in the URL path is `trips`. This URL returns a collection of trips which would be limited to trips to Atlanta. Note that you do not have to use the dot notation in this case. There are no sub-resources for trips. 

Context is the exception that allows you to filter multiple sub-resources (and even the resource) at the same time. For example, `https://api.byu.edu/domains/tutorials/cars?context=mileage&trips.destination=Atlanta*` could retrieve a collection of cars which have been used to make a trip to Atlanta. It would return the the `basic`, `odometer_logs`, and `trips` field\_sets. All of the `odometer_logs` for each car would be returned, but only the `trips` to Atlanta would be returned. You would have to refer to documentation on the API to know exactly which combination of contexts and sub-resource filtering works.

### Special Discussion: URL Segments

You will notice that we have attempted to treat the URL path segments in a consistent fashion.

`https://api.byu.edu/byuapi/<resource-name>/<key-to-resource>/<sub-resource-name>/<key-to-sub-resource>`

This allows URLs to be examined by automated processes. Exceptions are strongly discouraged.

### Special Discussion: Authorization

Resources and sub-resources should be organized to allow authorization policies to be enforced on the API. The `insurance_policies` sub-resource in this tutorial illustrates this principal. There is only one insurance policy per vehicle (in our example), yet we didn't combine the `insurance_policys` field\_set with the `basic` field\_set. This is because the authorization rules for the `insurance_policies` (who can view and modify this data) is significantly different than the `basic` field\_set. This simplifies the implementation of authorization policies and allows us to pull authorization out of the low-level business codes. For example, authorization policy can be enforced on the URL and http method only.

### Special Discussion: Derived Sub-Resources

Sub-resources can also be used to address another interesting and common business case. Sub-resources can be used to deliver derived or summarized information about a resource collection.

Allow me to contrive an example for our cars resource. Let's suppose that it is a common business case to summarize all of the vehicles in our ... (need to complete)

### Special Discussion: Where can I learn more about the Sub-Resources of an API?

The Swagger documents contain a tremendous amount of information about the API, and the WSO2 API Manager's "Try it out!" feature allows the consumer to explore the richness of the API. When deemed necessary, and in addition to the Swagger definition files, the API designer should create separate [Markdown](https://guides.github.com/features/mastering-markdown/) documents that might help the consumer understand the business flow of invoking certain API resource paths in succession in order to be effective as quickly as possible. Links to these documents are found in the "externalDocs" sections of the Swagger definition file.

These additional documents may address the following kinds of questions:

*   Which query parameters does a context "inherit?"
*   Which combination of sub-resource query parameters can be combined to filter the resource collection?
*   How do I perform common functions? How-to examples of the most common functions performed with the API help a consumer get started.

[Previous Example](/docs/consume-api/use-api/university-api-tutorial/university-api-tutorial-example-3)
