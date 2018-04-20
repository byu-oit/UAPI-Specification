## University API Tutorial - Example #2

The purpose of the second example is to:

*   Show how we include information about the answer
*   Introduce HATEOAS concepts
*   Demonstrate example #2 in the WSO2 API manager
*   Present the second example as a Swagger 2.0 document
*   Show how to use HATEOAS links to understand the resource
*   Show how to use HATEOAS links to develop your custom application
*   Explain how HATEOAS concepts can be tied to authorizations
*   Explain how HATEOAS concepts can enrich our additional documentation

### Metadata - Information About Your Answer

The first example returned a very simple data structure. For example, a collection of cars returned an array labeled cars:

```
[
  { the attributes for the first car in the collection},
  { the attributes for the second car in the collection},
  ...
  { the attributes for the nth car in the collection}
]
```

While this result is functional and useful, the University API returns a more powerful answer by returning metadata about the result. The following metadata attributes make sense with every collection. The API designer is free to add other metadata attributes when it makes sense. A client program could use this information to manage paging of large applications and give important clues in the user interface. For example, this information makes it possible for a user to jump to the end of the result set.

|metadata attribute|description
|-----|-----
|collection_size|How many items of the resource exist in the collection. This is not how many were returned in this "page" of the collection.
|page_start|The returned "page" of the collection starts on which item of the collection.
|page_end|The returned "page" of the collection ends on which item of the collection.
|page_size|How many items were returned in this "page" of the collection.
|default\_page\_size|Unless overridden in query parameters, this endpoint would default to the following "page" size.
|max\_page\_size|The largest "page" size that the implementation of the API will support.

To include metadata information in the answer, let's modify the standard JSON structure in the following manner:

```
{
 "metadata": {
    "collection_size": 3,
    "page_start": 1,
    "page_end": 3,
    "page_size": 50,
    "default_page_size": 50,
    "max_page_size": 1000
  },
  "values": [
    { the attributes for the first car in the collection},
    { the attributes for the second car in the collection},
    ...
    { the attributes for the nth car in the collection}
  ]
}
```

The JSON structure is now a complex object that consists of a metadata object (always labeled `metadata`) and the array containing our data (always labeled `values`).

### HATEOAS - Hypertext as the Engine of Application State

needs to be updated with more on the purpose of the links

The University API will also include hypertext links in the answer to make it more useful. To understand hypertext links, you may wish to familiarize yourself with HATEOAS concepts.

A quick explanation may be found at: [https://spring.io/understanding/HATEOAS](https://spring.io/understanding/HATEOAS)

A slightly more expansive explanation is found in the RESTful cookbook at: [http://restcookbook.com/Basics/hateoas/](http://restcookbook.com/Basics/hateoas/)

You may wish to look around a bit in the RESTful cookbook. It has some good, simple explanations. Both of these articles refer to a good explanation of the [Richardson Maturity Model](http://martinfowler.com/articles/richardsonMaturityModel.html). The article not only gives an explanation of HATEOAS, it also contains an explanation of how the University API constructs URLs and uses the HTTP methods.

This article explains the [JSON Schema for HyperText](http://json-schema.org/latest/json-schema-hypermedia.html).

To include HATEOAS links in the answer, let's modify the JSON structure again in the following manner:

```
{
  "links": {
     "cars__info": {
       "rel": "self",
       "href": "https://api.byu.edu/domains/tutorials/cars/v2"
       "method": "GET",
       "title": "cars__getCars"
     },
     "resource-name__business-operation": { Other links that apply to the car collection as a whole}
  },
  "metadata": {
    "collection_size": 3,
    "page_start": 1,
    "page_end": 3,
    "page_size": 50,
    "default_page_size": 50,
    "max_page_size": 100
  },
  "values": [
    { "basic": { 
        "links": {
            "cars__info": {
              "rel": "self",
              "href": "https://api.byu.edu/domains/tutorials/cars/v2/1CRTW25A3NX78232",
              "method": "GET",
              "title": "cars__getCar"
          },
          "resource-name__business-operation": { Other links that apply to the first car in the collection }
        },
        the attributes for the first car in the collection
    },
    { "basic": {
        "links": {
            "cars__info": {
              "rel": "self",
              "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962",
              "method": "GET",
              "title": "cars__getCar"
          },
          "resource-name__business-operation": { Other links that apply to the second car in the collection }
        },
        the attributes for the second car in the collection
    },
    { the links and attributes for the third car in the collection},
    ...
    { the links and attributes for the nth car in the collection}
  ]
}
```

We add the `links` section as a sibling property to the `metadata` and the `values` properties in the response. The links section contains links that apply to the collection as a whole. We also change the array of items in the `values` section to include links that apply to only that item in the array. Note that we labeled the the links and data attributes for each item in the collection as `basic`. This is a standard label that will be used throughout the University API. It will make more sense as we move through the rest of our examples.

### Updating the Cars API to include Metadata and HATEOAS Links

To see how this might look, let's return to the WS02 API Manager. Let's look at the API named Cars version v2. Cars v2 has the exact same operations as Cars v1 and behaves the same. The only difference is in the result sets. You can play with each of the operations to see the static example returns. (Remember to use "1CRTW25A3NX78232" on the operations that require a vin.)

Let's take a closer look at the results JSON for the GET '/' operation. It looks like this:

```
{
  "links": {
    "cars__info": { "rel": "self",
      "href": "https://api.byu.edu/domains/tutorials/cars/v2",
      "method": "GET",
      "title": "cars__getCars"
    },
    "cars__create": { "rel": "cars__create",
      "href": "https://api.byu.edu/domains/tutorials/cars/v2",
      "method": "POST",
      "title": "cars__createCar"
    }
  },
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
               "links": {
          "cars__info": { "rel": "self",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962",
               "method": "GET",
               "title": "cars__getCar"
          },
          "cars__modify": { "rel": "cars__modify",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962",
               "method": "PUT",
               "title": "cars__modifyCar"
          },
          "cars__sell": { "rel": "cars__sell",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962/salereceipts",
               "method": "POST",
               "title": "cars__sellCar"
          },
          "cars__delete": { "rel": "cars__delete",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962",
               "method": "DELETE",
               "title": "cars__deleteCar"
          },
          "owners__info": { "rel": "owners__info",
               "href": "https://api.byu.edu/domains/tutorials/owners/783783783",
               "method": "GET",
               "title": "owners__getOwner"
          }
        },
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
    { "basic": {
        "links": {
          "cars__info": { "rel": "self",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1DTGR14A6NP38861",
               "method": "GET",
               "title": "cars__getCar"
          },
          "cars__modify": { "rel": "cars__modify",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1DTGR14A6NP38861",
               "method": "PUT",
               "title": "cars__modifyCar"
          },
          "cars__sell": { "rel": "cars__sell",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1DTGR14A6NP38861/salereceipts",
            "method": "POST",
            "title": "cars__sellCar"
          },
          "cars__delete": { "rel": "cars__delete",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1DTGR14A6NP38861",
               "method": "DELETE",
               "title": "cars__deleteCar"
          },
          "owners__info": { "rel": "owners__info",
               "href": "https://api.byu.edu/domains/tutorials/owners/586586586",
               "method": "GET",
               "title": "owners__getOwner"
          }
        },
        "vin": "1DTGR14A6NP38861",
        "plate_state": "UT",
        "plate_number": "C49D3T",
        "vehicle_type": "Truck/SUV",
        "vehicle_year": "1997",
        "make": "Ford",
        "model": "Ranger",
        "color": "Blue",
        "owner_id": "586586586",
        "owner_name": "Daffy Duck",
        "created_by_id": "586586586",
        "created_by_name": "Daffy Duck",
        "created_date_time": "2014-05-11T13:45:53.09Z",
        "updated_by_id": "783783783",
        "updated_by_name": "Daisy Duck",
        "updated_date_time": "2014-09-15T08:12:44.03Z"
      }
    },
    { "basic": {
        "links": {
          "cars__info": { "rel": "self",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1CRTW25A3NX78232",
               "method": "GET",
               "title": "cars__getCar"
          },
          "cars__modify": { "rel": "cars__modify",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1CRTW25A3NX78232",
               "method": "PUT",
               "title": "cars__modifyCar"
          },
          "cars__sell": { "rel": "cars__sell",
               "href": "https://api.byu.edu/domains/tutorials/cars/v2/1CRTW25A3NX78232/salereceipts",
        },
        "vin": "1CRTW25A3NX78232",
        "plate_state": "UT",
        "plate_number": "L43S3T",
        "vehicle_type": "Truck/SUV",
        "vehicle_year": "2003",
        "make": "Chevrolet",
        "model": "Sierra",
        "color": "Tan",
        "owner_id": "586586586",
        "owner_name": "Daffy Duck",
        "created_by_id": "586586586",
        "created_by_name": "Daffy Duck",
        "created_date_time": "2014-05-11T13:45:53.09Z",
        "updated_by_id": "783783783",
        "updated_by_name": "Daisy Duck",
        "updated_date_time": "2014-09-15T08:12:44.03Z"
      }
    }
  ]
}
```

Let's break down the links that are returned for collection of cars. They are:

```
    "links": {
      "cars__info": { "rel": "self",
        "href": "https://api.byu.edu/domains/tutorials/cars/v2",
        "method": "GET",
        "title": "cars__getCars"
      },
      "cars__create": { "rel": "cars__create",
        "href": "https://api.byu.edu/domains/tutorials/cars/v2",
        "method": "POST",
        "title": "cars__createCar"
      }
    },
```

The `rel` parameter is extremely important in hypertext links. When it is `self`, it defines how to obtain this JSON result. The `href` parameter defines the URL, and `method` defines the HTTP method to invoke. The standard in the University API is to include a `self` link for the entire JSON result and, when possible, for each portion of the JSON result.

When the `rel` is not `self`, the University API will use a `resource-name__business-action` format. In our example, `cars__create` means that this link specifies how to add a car to the collection. The business-action of "create" should make sense to our business partners. For example, if we were working with a "classes" resource, some of the business-actions might be "schedule a class", "split a class", "merge a class", or "cancel a class". The business-action should be more meaningful than POST=create, GET=read, PUT=update, DELETE=delete (simply CRUD operations) (and more meaningful than our example).

The `method` and `href` attributes are self-explanatory.

We decided to use the `title` attribute in a slightly non-standard fashion. We include the `resource-name__operationId`. This specifies the code module that will actually process the web service invocation. In a Swagger 2.0 document, this is known as `operationId`. Code generators use these attributes in the Swagger documents. This provides useful documentation and capability for server-side programmers, but is not necessary for client-side programmers.

Please also note that we decided to name each of our link objects by the `resource-name__business-action` that usually appear in the `rel` attribute. (When `rel` is self, we use the full name of the link.) This makes it easier for programmers to manage the links. It looks a little different than most HATEOAS link examples (which are usually just an array of unnamed link objects), but it is a valid alternative defined in the JSON Schema standards.

Now, let's look at the links for the first item in the collection:

```
      { "links": {
          "cars__info": { "rel": "self",
            "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962",
            "method": "GET",
            "title": "cars__getCar"
          },
          "cars__modify": { "rel": "cars__modify",
            "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962",
            "method": "PUT",
            "title": "cars__modifyCar"
          },
          "cars__sell": { "rel": "cars__sell",
            "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962/salereceipts",
            "method": "POST",
            "title": "cars__sellCar"
          },
          "cars__delete": { "rel": "cars__delete",
            "href": "https://api.byu.edu/domains/tutorials/cars/v2/1FTBE14A4NP05962",
            "method": "DELETE",
            "title": "cars__deleteCar"
          },
          "owners__info": { "rel": "owners__info",
            "href": "https://api.byu.edu/domains/tutorials/owners/783783783",
            "method": "GET",
            "title": "owners__getOwner"
          }
        ],
```

The `self` link defines how to access this specific item of the collection. The `cars__modify` link describes how this item in the collection can be modified. The `cars__sell` link describes a specific type of business transaction on this car item. The `cars__delete` link describes how the car can be removed from the collection. The `owners__info` is an interesting illustration. An attribute of the car resource is the `owner_id`. This is the key to another resource, the `owners` resource. This link shows the possibility of accessing additional information about the owner of the vehicle from another API.

Some important concepts that should be highlighted in this illustration are:

*   Links should be tied to business actions, not HTTP methods. Of course, this means that multiple links could use the same HTTP method.
*   Links may point to other resources. This allows a very rich, dynamic "map" of the University API.
*   As a guideline in the University API, we use the same JSON structure to PUT or POST as was returned by the GET operation. This simplifies the mental model and the implementation code of the University API. When the API designer does make an exception and require different JSON structures for a business action, a link to the alternate JSON schemas is allowable in the link structure.

### Swagger Documents for Example #2

Example #2 may be explored in the WSO2 API Manager as the Cars v2 API.

To access the Swagger 2.0 specification for Cars v2: [https://raw.githubusercontent.com/byu-oit-appdev/api-repo-sandbox/master/swagger20/apitutor2/apitutor2.swag20](https://raw.githubusercontent.com/byu-oit-appdev/api-repo-sandbox/master/swagger20/apitutor2/apitutor2.swag20)

The only significant changes in the Swagger documents is in the model/definition section.

Please note that the Swagger documents specify the format of hypertext links, but it doesn't give us any clue to the possible value of these links. We'll talk about the possible methods to document this information later on this page.

### Special Discussion: Why HATEOAS Links?

One of the major advantages of HATEOAS links repeated in industry literature is that it enhances the discoverability of the API. In other words, a consumer that is exploring the API is given clues to the behavior of the API and "what they can do next." While this is true, good documentation could accomplish the same thing (perhaps even a little better). The reason that we would pass back the links with each result set is it gives the client application the ability to use this understanding to improve the user experience.

Perhaps an example would help.

Let's suppose that I am writing a client application that uses the Cars v2 API. By looking at the definition of the API and the different HATEOAS links, I can discover the capabilities of the Cars v2 API and what I should build into my user experience. I would have to account for the following behaviors:

*   Retrieving a list of Cars - including the ability to filter
*   Adding a new Car to the collection
*   Modifying a Car
*   Selling a Car - a transaction that may have some special business rules
*   Deleting a Car

I should tailor my client application to deal with these different transactions. For example, I may have an interface that lists a collection of Cars. The user could select a car that would present the attributes in a way that allows them to be modified and then saved. The list may also have a Delete button associated with each car or perhaps the Delete button is only presented on the page that presents all of the attributes for a single car (or both). I may also have an Add button on the car list page that presents a blank/defaulted page of attributes for a single car. Someplace I may have a Sell button that opens a special page that gathers the unique information to perform this specialized transaction. Sounds like our client application is coming together.

So, let's examine how we could use the HATEOAS links to improve the user experience. Let's suppose that there are business rules that govern which cars can be sold. This may be based on some data in the return set, but it may also be based on some data available to the server software, but not made available through the API. It is sort of a frustrating user experience if half the time that you press the Sell button you received an error message that this car can't be sold. Wouldn't it be better to gray-out or remove the Sell button for cars which are not eligible to be sold? Wouldn't it be better if this business logic was centralized in the API server code instead of replicated in every client application? The HATEOAS links perform this function. The University API will only include the HATEOAS links if the business function is actually available. In this simple example, the `cars__sell` link would only be included for cars that are eligible to be sold. All the client application has to do is manage the presentation of the Sell button based on the existence of the `cars__sell` link.

This same type of example extends to authorizations. Let's suppose that only certain users are allowed to add new cars. Other users are allowed to modify or delete some cars based on the attributes of the car. Wouldn't it be nice if the server-side API code also took authorization into account when including HATEOAS links? This would allow the client application to tailor itself to the capabilities of the user as well as the business rules that govern the data. (A carefully designed application should never have to tell the user that he is not authorized to perform the function that it made available.) The University API will also look at the authorizations of the user to determine which HATEOAS links to include.

There is also one less significant advantage to using HATEOAS links. If client developers use the URLs in the links instead of hard-coding them into the client application, the server-side API programmer is free to modify the URL without impacting the client program. Of course, this does not allow the server-side programmer to modify the request body/return body, but it does remove the URL from the "API contract."

### Special Discussion: Designing the API to Leverage HATEOAS Links

The previous example illustrated the power of HATEOAS links to enhance the client program, but could we do even more? Perhaps the API designer could build even more power into the links.

An example to illustrate the point:

I have an API that returns classes in which a student is enrolled. A possible business function for each enrollment is a "drop", as in dropping or withdrawing from the class. We could include an `enrolled-class__drop` link for each class in the student's schedule. However, there are certain business rules that govern the dropping of a class. First, there are calendar considerations. A class can't be dropped after a certain point in the semester. Obviously, we would not include the `enrolled-class__drop` for classes that have passed this point in the calendar. There are also quite a few authorization rules around who may drop a class. Obviously, the student or one of the student's delegates may drop the class; so may certain counselors and administrators depending on which major the student has declared or which college is offering the class. Since these rules can be quite complex, it would be nice if the server-side API code was making this determination and simply including the `enrolled-class__drop` link when appropriate. This seems pretty obvious.

But there are some more pretty complex business rules involved in dropping a class. For example, the class may be a prerequisite for other classes in subsequent semesters. The drop could create consequences that we should bring to the attention of the user prior to the drop. What if the API designer also planned for an `enrolled-class__drop-with-prerequisites` link that could be used in this case instead of the `enrolled-class__drop` link. It would immediately notify the client application of this case. The designer may even include a second `enrolled-class__drop-impact` link that could be used to retrieve all of the possible impacts of the drop. This would allow the client application to notify and inform rather than reacting to the drop request with errors or a complex workflow. This type of functionality could be used for other consequences of a drop, such as loss of financial aid, scholarship, or direct impact on a graduation application. The advantage of this approach is creating better client applications without having to clog them up with complex workflow logic.

### Special Discussion: Where can I learn more about the HATEOAS links for an API?

Developer portal documentation

The Swagger documents define the format for a Hypertext link and what HATEOAS links are returned by an API, but it is difficult for the Swagger document to convey the business meaning. When deemed necessary, and in addition to the Swagger definition files, the API designer may create separate [Markdown](https://guides.github.com/features/mastering-markdown/) documents that will help the consumer understand the business flow of invoking certain API resource paths in succession in order to be effective as quickly as possible. Links to these documents are found in the "externalDocs" sections of the Swagger definition file.

These additional documents may address the following kinds of questions:

*   How should the request body/query parameters be formed for a specific business function?
*   What implications should certain HATEOAS links have on the user experience?
*   What authorizations/business rules are associated with certain HATEOAS links?

[Previous Example](/docs/consume-api/use-api/university-api-tutorial/university-api-tutorial-example-1)   [Next Example](/docs/consume-api/use-api/university-api-tutorial/university-api-tutorial-example-3)
