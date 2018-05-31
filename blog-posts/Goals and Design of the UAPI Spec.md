## The Method to the Madness - Goals and Design Decisions of the University API Specification

### First a little history...

OIT has been providing web services for close to ten years. When we started SOAP services were all the rage and REST was the new "shiny" way of doing things. Our first custom web services were SOAP, complete with lots of XML and the wonderful WSDL files nobody could understand. As our organization's use of web services started to mature teams were able to find quick and dirty ways of providing services to meet specific user needs. Along the way our C-Framework was tweaked to allow any screen built with the framework to be turned into a (RESTish) web service with minimal effort. And our web service management software was able to (poorly) turn SOAP services into REST and vis versa. With all these developments at its peak we had over nine hundred services in our registry. 

**That's great! Right?**

Unfortunately for consumers of these web services finding the service that performed the desired task was nearly impossible. Some desired data elements were available from multiple web services. Determining which to use required a discussion with the teams providing the services. Other data elements were scattered across multiple services requiring multiple calls to gather all of the desired data, contributing to "chatty" applications that performed extremely poorly. The identifiers used to access data varied across web service providers and sometimes within the same provider's web services. Terminology and field names also varied from service to service depending which system was providing the web service. Consumers were intimately aware of which underlying university systems were providing each of the web services they were using and were on their own to reconcile the differences between systems. 

**So why'd we do it?**

Providing our consumers with web services did allow for advances in applications and services to our customers that were not possible any other way. One of the first examples of the power of web services that truly changed the game for our users was the BYU mobile app. All access to back end data used by the mobile app is done through web services. If you've ever wondered why some features of the app are native and others are simply links to web pages the answer (most of the time) is simple - if there isn't a set of web services then the link is the best we can do. Sometimes that is perfectly ok. Other times it is a extremely poor user experience but it is the best we can do.  

**So what caused the problem?**

There were a number of issues that created the problems with using our web services. At the time we created our first registry we believed it to be useful to provide both SOAP and REST versions of the services we published. Over time it became apparent that for our purposes REST was clearly preferred and the usage of our SOAP services was limited to cases where technology limited a consumer's options. Over time most of those limitations disappeared. 

Another issue was that many of the services we were providing were simply a layer over web pages. The design goals and maintenance patterns of web pages and web services differ greatly. These web services were also tightly coupled to their corresponding pages to the extent that changes to the page would cause changes to the corresponding web service, potentially breaking the service consumers. The teams maintaining the web pages didn't have any good way to find out who was using their web services to coordinate such changes.  

Perhaps the most problematic issue is the tight coupling between consumers of our web services and the systems providing those services. Any changes to the providers could potentially cause the consumers to have to adjust their client code to match. The need to coordinate changes with what could be a large number of consumers can become an additional burden to the teams providing the services. 

**So how do we solve the problem?**

Some of the above problems were fixed with better understanding of our usage patterns and newer technology. The number of web services in our registry has been cut by over half by eliminating our SOAP web services and deprecating other services. Our new API management tools implement a subscription model that requires all web service users to register before any calls to services are allowed. This enables maintenance teams to know who is using their services and how. 

Those were the easy problems to solve. The tight coupling between consumers and the systems producing the web services they use requires a more complex solution. There are a number of well understood patterns in the industry that can help solve the problem. Perhaps the most effective is to place a facade between the consumers and producers that provides a level of isolation between the two, thus insulating the consumers from changes to the underlying systems and allowing those systems to evolve over time. The facade gives the producer the chance to present a well thought out interface [^interface] to all consumers that is optimized for consumption via web services and reflects the actual business functions the system performs. 

[^interface]: You'll notice that this is the first time I use the term *interface* and I haven't used the term *API* at all (yet). That is because an interface and by extension an API imply that there is a consistency across the entire set of methods contained within that interface. That isn't the case for the majority of the web services in the registry. A set of web services may have some design points in common by virtue of the underlying source system but only a relatively few were consciously designed to provide a complete and consistent experience for the consumer.  

The University API Specification is an attempt to bring consistency to the interface such a facade would present to consumers.

### The University API Specification

**What is it?**

The University API (UAPI) specification is the result of a years long design effort. The effort focused on creating a specification that could be used for implementing APIs that present to consumers important university business functions. 

**What is it trying to accomplish?**

The UAPI effort defined a REST interface specification that attempted to meet a specific set of goals[^universityapi].  

[^universityapi]: See <http://www.windley.com/archives/2014/09/a_university_api.shtml> for the original discussion of the UAPI and its design goals.

>
- **Business-oriented** - the API should be understandable to people who understand how a university works without having to understand anything about the underlying implementation. Many more people know how a university works than could ever know about the underlying implementation. An API based on resources familiar to anyone who understands a University makes the API useful even to non-programers.  
- **Consistent** - a developer should see a consistent pattern in URL formats, identifiers, data formats, and error messages. Consistency allows developers to anticipate how the API will work, even when they're working with a new resource.  
- **Completeness** - over time the University API ought to be an interface to every thing at the University that works via software (which is to say everything).  
- **Obvious** - using the API should be obvious to anyone who understands the general principles without needing to rely excessively on documentation.  
- **Discoverable** - a program should be able to discover allowed state transitions, query parameters, and so on to the extent possible.   
- **Long-lived** - An API is like a programming language in that it is a notation, not a technology. The goal is to create something that is not only intuitive, but stands the test of time. Designing for long-term use is more difficult than designing for short-term efficiency 
- Model the actual structure of University resources as accurately as possible. Contracts are consistent for all consumers.  
- Isolate clients from the implementation details of and changes to source systems along with eliminating boundaries between systems.   
- Provide a consistent client experience across resources  

**Why REST? Why not X[^X]?**

[^X]: Fill in your favorite "shiny" new technology here...

We realize there are many alternatives to using REST. We have chosen to use REST as the base for the UAPI for some very specific reasons. 

First and foremost REST[^rest] has been around for as long as we've been doing APIs. Over the years REST has become well understood even if the term is somewhat abused. Many APIs are RESTish but not REST. Our goal was to take advantage of as many of the REST principles as we could in order to fully benefit from its promise.  Among the benefits we expect to enjoy from using REST are:

* **Resource based** - Modeling our business data and operations as resources in most cases naturally aligns with the actual business of the university. Simply surfacing underlying system data structures and operations does not provide the level of abstraction that we desire the UAPI to achieve.
* **Addressability** - All instances of resources are directly addressable via a consistently constructed URI.  
* **Uniform Interface** - REST describes a uniform contract for interacting with resources. This simplifies the discoverability and operation of the interface. The uniform interface includes:
    * **HTTP methods** - All operations against the resource is expressed via the standard HTTP methods. 
    * **HTTP status code** - The status of all requests to the resource are expressed in standard HTTP status codes. 
    * **State transitions** - The interface includes information about the next states the resource is allowed to transition to. Simply put, the provider informs the consumer which operations the consumer is allowed to perform on the resource. This is usually accomplished by using Hypertext As The Engine Of Application State (HATEOAS) links. 
* **Statelessness** - Tracking server side application state between operations creates all sorts of headaches for both the provider and consumer. REST requires that all operations be stateless. 
* **Connectedness** - Because all resources are addressable resource responses can and should include the URI for any resource they reference. This removes the responsibility from the consumer to have to be aware of how to construct URIs for any referenced resource and allows the provider freedom to adjust resource addresses as necessary. 
* **Contract based** - In REST the URI defines the contract between provider and consumer. Resource representations are consistent for all consumers based upon the URI. Responses may vary based upon mime-types requested or query parameters. Those variations are considered part of the contract. 
* **Tooling** - REST is well understood in the industry and thus has support in virtually all programming languages. A robust set of definition languages have been developed. We have standardized on the OpenAPI Specification [^openapi] (formerly known as Swagger). There are many mature API management and security tools available as well.   

We have chosen to measure our implementations against the Richardson Maturity Model[^richardson].

[^rest]: For a more in depth discussion of REST see <https://restfulapi.net>
[^openapi]: <https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md>
[^richardson]: Leonard Richardson is one of the authors of the *Restful Web Services*. For the definition of the Richardson Maturity Model see <https://restfulapi.net/richardson-maturity-model/>. For a more in depth discussion of Leonard's experiences see <https://www.crummy.com/writing/speaking/2008-QCon/act3.html>. 

**But 96.7% of our users only want read only access so we should use X instead.**

Yup. Most of the access requested to UAPI data will be read only. But there is also that 3.3% that need update access. So we have choices to make. We could provide a read only interface in X that will meet those read only needs and then provide another interface in something else (call it Y) that handles the update requests. But then we now have two interfaces to maintain. And do we ask those that want update access to use X for reads and Y for updates? Or do we have to create a fully read/update version of the interface in Y and do X? We chose to provide a single consistent interface for both read and update. 

**But the UAPI spec is so...**

Wordy? Strict? Bloated? Boring? Hard?

We realize that implementing the spec is not nearly as easy on producers as if we just continue in our free wheeling web service creation ways. Implementing the spec will most likely require engineers to start looking at how they develop their web services differently. We believe the benefits are worth the effort. Here are some of the ways we see things changing:

- **An emphasis on design first development.** Well designed resources are more than just database tables with services on top of them. We expect that the designers of the resources will anticipate the most common use cases and design for them. This can help reduce the "chatty" consumer/provider interactions we see with many of our current web services. 
- **Consolidation of authorization rules.** The requirement to provide the consumer with the possible operations they can perform on a given resource requires that the code that implements business rules around authorization be designed differently. Splattering authorization through out the provider code isn't going to work anymore. This consolidation moves us closer to be able to take advantage of external rules engines such as ABAC in the future. It also should make understanding and modifying business rules around authorization easier - at least they will be in one spot so that's a start. 
- **Prevent as much as possible business rules, especially around authorization, from leaking into the consumer code.** For example, the mobile add/drop client knows nothing about all the rules around when and if a student can add a particular class they are viewing. The only logic it has to implement is that if the "add" HATEOAS link is in the response then offer the student the opportunity to add the class. If the student chooses to add the class then use the provided link for the add. All of the logic about calendars, prerequisites, holds, etc is contained on the provider where it belongs. Any changes to add/drop policy are implemented on the provider and the client doesn't change at all.  
- **Eliminate, as much as possible, the need for consumers to build links to UAPI resources.** The more consumers have to build links, or worse, hard code them, the more tightly coupled the consumer becomes to the provider. We realize that a consumer has to start someplace and so some links, top level resources, etc. will be embedded in the application. But locations of ancillary data such as code tables for select boxes should be provided in the response. 
- **Emphasize end user experience over development effort** - Providing the consumer with as much information as possible about next possible actions (via HATEOAS links) and property level metadata allows the consumer to provide a much better experience for the end user. Allowing the user to attempt to perform a business function only to be denied when the client attempts the update provides a poor experience for the user. We can't prevent this from ever happening - systems are dynamic and the state may have changed between the original call and the attempt to update - but we can be better than we are now. 

**But nobody could possibly want to ever do X with my API?**

"Nobody" and "ever" are loaded terms. When we first started down the web services path we heard a similar refrain from many of our engineers. Very few anticipated what a difference APIs would have on our operations. On a normal day our API Manager processes more than four million transactions. On a busy day that number can be many times that. 

When we restrict what our APIs can do because we assume nobody would ever want to perform some operation we have created a self fulfilling prophecy. We have to strike a balance somewhere between enabling innovation and trying to implement every conceivable feature. We trust that our engineers can find that balance. 

**But the responses are so large!**

In order to accomplish the above goals the response has gotten (relatively) large. One of the most expensive parts of any web service call is going across the wire. So minimizing the number of calls required by returning more (and possibly extra) data is usually more efficient. We also designed the responses to be consistent and modular which also increased their size. If for some reason a client can't handle the size of the response (like Kelly's Arduino) then a pattern such as Backend for Frontend (BFF) can be used to front the UAPI and provide a custom experience. Given that even the least powerful mobile device available today is much more powerful than the laptops we had just a few years ago we believe that most clients will be able to process the responses sufficiently. 

**But it is going to be slow!**

We realize that each individual API call will be slower than our more traditional web service calls. There is more work for the provider to do for each call. But overall we expect as we become more familiar with newer code design patterns such as scatter/gather the providers will become more efficient and response times will improve. We also expect that the computing resources available to providers will also continue to expand. Clients also have access to much more powerful compute resources and parallel processing on the client side is also the rule rather than the exception.


###I really don't like X and nobody will ever use it?

There are a few specific parts of the resource representation that seem to cause the most consternation from providers. Lets address why we made the decisions we did:

**I hate HATEOAS links!**

Probably the biggest complaint we get is about the links section of the response. The intent of the links section is to convey to the consumer what the allowable state transitions for the resource are (sorry for going all "state machine" on everybody). In reality (and aligning with the principles of REST) we wanted to convey to the consumer what they could do next based upon the business rules associated with this specific user and this specific resource at this instance in time. This helps accomplish two things - provides a better experience for the user and keeps business logic from creeping into the consumer. We could have chosen to simply provide a list of next possible operations but then the consumer would be on their own in determining how to perform the operations available to them. We chose instead to use HATEOAS links as the way to provide that information to the consumer. The advantage of using links is that where possible the consumer can avoid building links on their own. In some cases simply calling the supplied HTTP method on the link is the only action the consumer needs to perform such as in the case of using the DELETE method to drop a class from a student's schedule. 

We also embedded links in the metadata that is returned with the properties of a resource. The intent is to remove as much as possible the need for the consumer to be aware ahead of time the locations of other resources related to the current resource. This gives the ability to make some changes in the future without having to coordinate those changes with every consumer. 

**All I wanted was the value of a property! What's all this other stuff?**

I stated above that one of the goals we had with the UAPI was to model resources that reflect actual business entities and not just surface the underlying technology. This extends all the way down to the properties of a resource. Maybe an example will help. We have this thing in a number of systems called "state\_code". It has in it a two letter designation for US states and Canadian provinces. Along with the "state\_code" we have a "state\_code\_desc" that give the actual name of the state. Are these really two properties? No. They are two properties of a "state" - the code we store in the underlying systems and the actual name of the state. So why do we have the two properties? Because the majority of our systems are based upon relational technologies that don't handle objects like "state" very well.  We wanted to do better in the UAPI. So the resource has a property named "state" with a sub-property named "value" (a standardized name for "state\_code") and "description" (a standardized name for "state\_code\_desc"). 

 We also have a number of properties that have a restricted set of values they can assume. We provide links to the URLs of services that the consumer can use to determine what the acceptable values are. The consumer can use the link provided to present the end user with the possible values for the property. As the acceptable values change the choices presented to the end user automatically change with no effort from the consumer. 
 
 Finally we have other information about properties that may be useful to some consumers such as if the property is part of the identifier for the resource, if it is generated by the underlying system, and whether the end user can modify the property. Not all consumers need this level of information. Consumers are free to ignore the information they don't need. 
 
 **Links and Metadata and Values! Oh My!**
 
 Yes there are links and metadata sections scattered all over the response. But if you look carefully you'll notice that they follow a definite pattern. Collections of top level resources have links and metadata. Those links and metadata contain operations and information specific to operating on the entire collection (paging, etc). Top level resources have their own links and metadata that are specific to that individual resource. Sub resources have their own links and metadata for (you guessed it) working with that sub resource. The representation of a resource will be consistent regardless of how it was obtained - as an element of a collection, an individual resource, or a direct query to a sub resource. While this makes some metadata properties redundant it ensures that the code processing the response can be completely oblivious to how the resource was obtained, thus making code modularization (on both the provider and consumer side) easier. 
 
 **What about my third party app that can't handle this stuff?**
 
 We realize that each third party app has its own way of doing things and they aren't going to adjust to the UAPI or any other spec. That is where other facade patterns like the Backend for Frontend come into play. But if those facades go against UAPI resources instead of hitting domain specific web services directly they reap as many of the benefits of the UAPI facade as possible. 
 
### But nobody will ever use it!

This has been a common complaint about a number of the parts of the UAPI. We've never given the consumers the kind of information in a resource response that we are with the UAPI. We believe that over time the way consumers of our APIs build their applications will change to take advantage of the new information being provided. We do know that if we don't give our consumers this type of information they will never be able to take advantage of it. You can't use what you don't have.   

