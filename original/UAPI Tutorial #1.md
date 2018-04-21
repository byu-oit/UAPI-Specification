## University API Tutorial - Example #1
The purpose of the first example is to:

*   Introduce vocabulary
*   Introduce our understanding of a RESTful API
*   Demonstrate the example in the WSO2 API manager
*   Present the example as a Swagger 2.0 document
*   Explain how the results set can be filtered
*   Explain how we use the URL to access a specific resource
*   Demonstrate how we will provide additional documentation

### BYU's University API is a RESTful API

To understand the University API, you're going to have to get comfortable with RESTful APIs. There are many excellent articles and books on the subject. For example, simply google "RESTful" and there are dozens of good links including a link to Roy Fielding's dissertation that introduced the concept of Representational State Transfer (REST). This tutorial won't attempt to review all of this information. It assumes that you will gain a working understanding of RESTful APIs on your own.

At the same time, you don't have to be a REST guru to understand and use the University API. Most of the concepts are intuitive and easily understood. You may wish to get started with the tutorial and refer to other additional information on REST as needed. The tutorial does provide links to some useful reference information that you may wish to explore.

So, let's get started.

A RESTful API is based on the HTTP protocol and is built around the concept of resources. The URL describes a resource or a **collection** (set) of resources. For example, `https://host-name/cars` describes a collection of cars. If you were to invoke an HTTP GET method against this URL, you might expect to receive a collection of cars in a JSON array. Each car in the array represents an **item** or **instance** in the collection. A URL of `https://host-name/cars/{vin}` would describe a specific car. If you were to invoke an HTTP GET method against this URL, you would expect to receive only one instance (or perhaps the empty set if the vin is not valid). Sometimes a collection is so large that it is difficult to use. It is possible to filter a collection with the query string portion of the HTTP protocol. For example, `https://host-name/cars?color=red&make=Ford` would return a JSON array that only contains red Fords.

The University API makes extensive use of the HTTP protocol. This includes HTTP methods, HTTP return codes, and HTTP headers.

For a better understanding of HTTP methods, you may wish to refer to [this guide provided by W3 Schools](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html). (Note: Pay special attention to idempotent methods. The goal of the University API is for all methods to be idempotent.)

The following table describes how the University API utilizes HTTP methods.

|HTTP Method|description|notes
|-----|-----|-----
|OPTIONS|Returns the set of HTTP methods allowed for the URL that addresses a collection.|According to the HTTP protocol the answer is returned in the HTTP `Allow` header. For example, if a URL allows the GET and POST methods the HTTP header would contain `Allow: GET,POST,OPTIONS`. The University API also returns a JSON array in the body that answers the question. The JSON would look like this:`{ "supported_methods": ["GET","POST","OPTIONS"]}`
|GET|Returns a JSON object in the body of the response.|The JSON object may be either a single instance or a collection of resources. Sometimes, a combination of query string filters or URL segments may return only one (or even zero) items. That's OK. Even the empty set is a valid set. In the University API when a query string containing a filter is applied we have elected to always return a JSON array structure even when there is only one or zero items returned. This allows the developer to code for a predictable JSON data structure. We don't change the contract of the return body based on the number of items selected.
|POST|Creates a new instance of a resource.|In most instances the body will contain a single item to be added but the request body may contain a JSON array structure of resource(s) to be created (added). The return body is the item that was added or a JSON array structure that contains information about each resource for which an add attempt was made.
|PUT|Modifies the resources defined by the URL based on the contents of the request body.|While there may be cases where we perform bulk updates, we have tried to avoid them in the University API. Generally the URL defines a single item instead of a collection and that single item is modified to "look like" the resource in the request body. The modified resource is generally then returned in the return body.
|DELETE|Delete the resource defined by the URL|Once again there may be cases for bulk deletes, but we have tried to avoid them in the University API. Since the URL completely defines the resource to be deleted, there is generally no request body or return body for this method.

For a better understanding of HTTP return codes you may wish to refer to[ this page at the REST API Tutorial site](http://www.restapitutorial.com/httpstatuscodes.html). (While you are there you may wish to look around at some of the other information. It is a pretty good reference.) The University API will use the proper HTTP return codes to convey information.

For a better understanding of HTTP headers you may wish to google "http headers". There is an overwhelming amount of good information. The main use of HTTP headers in the University API is to convey authentication/authorization tokens.

### The WSO2 API Manager

A key component in documenting and managing the University API is WSO2's API Manager. It is a powerful and extensive tool. WSO2 provides excellent documentation and tutorials.

This tutorial will only introduce you to the tool by presenting examples in the API Manager. Let's get started.

1.  Open another tab in your browser. You will want to continue reading these instructions as you work in the other tab.
2.  Go to "[https://api.byu.edu/store](https://api.byu.edu/store)" to access the first example in the API Manager.
    1.  You should be able to sign in (Login button is in the top right corner) with your net_id and password. However, you are not required to sign in to review example #1.
3.  You should be presented with list of APIs (tile format). 

![](/sites/developer.byu.edu/files/Tutorial-Updated1.PNG)

Don't get confused if the screen looks a bit different. The APIs defined in our WSO2 instance is constantly changing. Find the "Cars" API and left click on it. You should be presented with the following:

![](/sites/developer.byu.edu/files/Tutorial-Updated2.PNG)

The most important thing on this page is the base URL that you would use to invoke the API, which is `https://api.byu.edu/domains/tutorials/cars/v1`. For the point of this tutorial, we only published one URL for Production and Sandbox. When dealing with a "real" API, there will probably be two URL's. The parts of the base URL are:

1.  `https:` - The choices are really only https or http. Most of the time, the University API will require you to use https.
2.  `//api.byu.edu` \- This is the host-name (which may include a port).
3.  `/domains/tutorials` - This differentiates APIs of the University API from other provider APIs.
4.  `/cars` - This is the resource-name.
5.  `/v1` - This is version 1 of the API. 

To explore the API, select the "API Console" tab in the middle of the page. You should be presented with the following:

![](/sites/developer.byu.edu/files/Tutorial-Updated3.PNG)

Let's explore one of the operations to get a feel how this works. Left click on the "GET /{vin} Retrieve a Car" operation. You should be presented with the following:

![](/sites/developer.byu.edu/files/Tutorial-Updated4.PNG)  
![](/sites/developer.byu.edu/files/Tutorial-Updated5.PNG)

This tells you what parameters are required to invoke this operation and whether they are required are not. It also details what return codes can be expected. To get an idea how WSO2 functions, enter "1CRTW25A3NX78232" as the vin value (by replacing the "{required}" text in the input field) and press the "Try it out!" button. You should be presented with the following:

![](/sites/developer.byu.edu/files/Tutorial-Updated6.PNG)

There are two cool things to be learned from this. First, what is the request URL. The developer can see exactly what needs to go into the custom application. Second, an example of the JSON structure that will be returned.

Go ahead and play with the rest of the operations.

### Swagger 2.0

need to add developer portal section

WSO2 provides an interface to define an API. It is OK for learning how WSO2 works, but we will not use it to define the University API. WSO2 also provides the ability to import Swagger 2.0 documents. Swagger is a specification used to define RESTful APIs. There are several advantages to defining the API with Swagger documents, but the most convincing one is that the Swagger community has provided [code generators](http://swagger.io/swagger-codegen/) for most programming environments and other utilities to help the consumers of the API. Future tutorials may demonstrate how to generate code from a Swagger document. The Swagger 2.0 specification is the [current specification](http://swagger.io/specification/#exampleObject). (You will note that the Swagger 2.0 specification has also been adopted as the official specification of the Open API Specification.)

The Swagger 2.0 document for example 1 can be found at: [https://raw.githubusercontent.com/byu-oit-appdev/api-repo-sandbox/master/swagger20/apitutor1/apitutor1.swag20](https://raw.githubusercontent.com/byu-oit-appdev/api-repo-sandbox/master/swagger20/apitutor1/apitutor1.swag20)

If you would like to see a Swagger 2.0 document rendered in an editor, this is how you can do it:

1.  Open another tab in your browser. You will want to continue reading these instructions as you work in the other tab.
2.  Go to "[http://editor.swagger.io/#/](http://editor.swagger.io/#/)" to access the Swagger 2.0 editor
    1.  If you have never been to the Swagger 2.0 editor, it will come up with an example API. If you have, it will present the last API you were viewing.
    2.  In either case, you may blank out the editor by selecting "Blank Project" on the "File" menu.
3.  Start loading the Cars tutorial API by selecting "Import URL" on the "File" menu.
4.  When the Swagger dialog box appears, paste the URL above into the "URL" field.
5.  If the URL is valid, the "Import" button will be highlighted on the Swagger dialog box. Press the "Import" button.
6.  Depending on the size of the Swagger 2.0 document, it may take a moment to render the API.

The Swagger 2.0 editor allows you to explore the API side-by-side with the Swagger 2.0 document. The WSO2 Publisher uses this same editor when creating an API.

## Special Discussion: How can I filter a large resource collection?

The University API will follow the pattern of this example for all resources.

*   A GET method executed on a URL of `https://api.byu.edu/byuapi/plural-resource-name` will return an JSON array that contains a collection of resources.
*   A GET method executed on a URL of `https://api.byu.edu/byuapi/plural-resource-name/:resource-key` will return that specific instance of the resource.

This sounds great as long as I am working with resources that only contain a few dozen items. Of course, many of the resources in the University API will contain millions of items.

One method to address this is to filter the collection with query parameters. For example `https://api.byu.edu/domains/tutorials_/cars/v1?color=red&make=Ford` would return a JSON array that only contained red Fords. There may be a few APIs in the University API that allow you to filter on any attribute in the return set, but this would be the exception. In most instances the query parameters that are available and how they can be used are defined by the implementation in the API. The API implementation is built to ensure the performance that would be expected by every consumer of the API.

The query parameters available on an endpoint (HTTP method/URL path combination) are documented in the Swagger documents and can be viewed in the WSO2 API Manager. For example the GET method on the `https://api.byu.edu_/domains/tutorials/cars/v1` endpoint supports a large set of query parameters. It looks like this in the WSO2 API Manager:

![](/sites/developer.byu.edu/files/Tutorial-Updated7.PNG)  
![](/sites/developer.byu.edu/files/Tutorial-Updated8.PNG)

 In this case most of the query parameters are optional. If a query parameter is required, a "(required)" indicator will appear in the text box. The `page_start` and `page_size` query parameters default to 1 and 50 respectively, but may be modified. You may use the "Try it out!" function to see how your URL should be formed, with certain limitations (and possible bugs).

For example, if I type "UT" in `plate_state`, "ford" in make, and "red" in color and press the "Try it out!" button, the request URL should look like this:

`https://api.byu.edu/domains/tutorials/cars/v1/?plate_state=UT&make=ford&color=red&page_start=1&page_size=50`

Even with filtering, it is still possible for a result to be so large that it is difficult to work with. Let's suppose that we have 800 red fords with a Utah license plate in our database. A common API technique is to "paginate" large results sets. In our example where `page_start`=1 and `page_size=50`, the first 50 items of the result set would be returned. A second invocation where `page_start`=51 and `page_size`=50 would be required to retrieve the next 50 items of result set. Caution should be exercised when implementing or using a "pagination" technique. If it is possible that the result set can change between invocations, it is possible that the page boundaries can change and items will be duplicated or dropped in the result set.

## Special Discussion: How can I address a specific item in a resource collection?

You can address a specific instance of a resource by including the resource-key in the URL segment directly after the resource name. For example, to access a specific car with a vin of "1CRTW25A3NX78232", the URL is `https://api.byu.edu/domains/tutorials/cars/v1/1CRTW25A3NX78232`.

There are some important concepts in the University API that should be understood here.

*   In a RESTful API, every resource has one and only one key. We will follow that concept in the University API. In our example we decided to use the vin attribute as the key to the cars resource. Other attributes may also uniquely identify a car (for example, the combination of plate\_state and plate\_number would uniquely identity a car), but they may not be used in a URL segment because of the confusion that would cause. If you wanted to access the car with the plate\_state of "UT" and the plate\_number of "A452ZK", then you would have to use the URL `https://api.byu.edu/domains/tutorials/cars/v1?plate_state=UT&plate_number=A452ZK`.
    *   In the past, many of BYU's APIs would allow you to send in person\_id or byu\_id or net\_id or... to identify an individual. This complicated implementation and sometimes led to strange cases. The University API cleans up this problem by requiring the API designer to select one of the attributes as the key that can be used as a URL segment and the others can only be used as query parameters.
*   For the sake of consistency in the University API, if a URL segment follows the plural-resource-name, it may only be the resource-key for that resource-name. For example, `https://api.byu.edu/domains/tutorials/cars` is OK and `https://api.byu.edu/domains/tutorials/cars/v1/1CRTW25A3NX78232` is OK, but `https://api.byu.edu/domains/tutorials/cars/anything-else-other-than-the-vin` is not allowed.
*   We do allow the key for a resource to be a combination of attributes. For example, the API designer of the Cars API should be able to choose plate\_state+plate\_number as the key if it makes business sense. In this case the resource key is still only allocated one URL segment and the attributes are separated by a comma. In other words, if the resource key is plate\_state+plate\_number, the URL to access a specific car would be `https://api.byu.edu/domains/tutorials/cars/UT,A452ZK`. The order of the key attributes is important and is specified by the API designer. It would not be acceptable to make each attribute of a multiple attribute key a separate URL segment (`https://api.byu.edu/domains/tutorials/cars/UT/A452ZK` is not an acceptable choice).

## Special Discussion: How do I know what the API will return?

Needs to be updated

The Swagger documents contain definitions using standard JSON schema to define the request/return bodies associated with each API endpoint. These models are visible in WSO2 API Manager. We encourage developers to use the Swagger 2.0 documents and code generator tools available at the [Swagger communities web site](http://swagger.io/). This approach make definition and management of the request/return bodies much easier and less error-prone.

As a special note here, and as a guideline, the University API will use the same JSON data structure to PUT or POST information as was returned by the GET operation. In essence, we try to follow the concept that if I GET some information then I should be able to use the same JSON data structure to modify it. One advantage is that this simplifies the mental model required to understand and use an API. A second advantage is that a programmer doesn't have to "translate" from one data structure to another in order to make modification. Hopefully, this simplifies coding and reduces errors.

## Special Discussion: Where can I learn more about an API?

need to add developer portal

The Swagger documents contain a tremendous amount of information about the API, and the WSO2 API Manager's "Try it out!" feature allows the consumer to explore the richness of the API. When deemed necessary, and in addition to the Swagger definition files, the API designer should create separate [Markdown](https://guides.github.com/features/mastering-markdown/) documents that might help the consumer understand the business flow of invoking certain API resource paths in succession in order to be effective as quickly as possible. Links to these documents are found in the "externalDocs" sections of the Swagger definition file.

These additional documents may address the following kinds of questions:

*   What are the valid domains of the parameters? - this should include URLs to an API that returns the set of valid values.
*   What special capabilities may be associated with a parameter? For example, can wildcards be used with a parameter. A search for a partial license plate could be executed by using wildcards in the plate_number parameter. "A45??K" or "A45*" could be valid entries if wildcards are implemented in the API. An other possible capability could be lists of values. A search for blue or red vehicles could be implemented and the entry in the color parameter would be "blue,red".
*   What combinations of parameters could be valid and which might be invalid? For example, our example API could be implemented in such a way that it will do a license plate search (even a partial search) or a search by vehicle year, model, make, and/or color, but the API does not support a search on a partial license plate and vehicle year, model, make, and/or color. One may ask if this restriction makes sense, but that is exactly the point. The consumer needs to understand what is implemented in the API especially when it is not intuitive.
*   What additional error information will be returned by the API? When an error is detected, the API will use the http defined error messages, but it will usually also return more error information in the body of the return.
*   How do I perform common functions? How-to examples of the most common functions performed with the API help a consumer get started.

[Next Example](/docs/consume-api/use-api/university-api-tutorial/university-api-tutorial-example-2)
