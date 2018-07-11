# The University API Specification and Domain Services

## Introduction
The University API (UAPI) specification is intended to bring a level of standardization to APIs that OIT provides for cross-domain access to data. The UAPI specification can and should be used in one form or another by domain APIs. The purpose of this document is to provide guidance to domain teams.

## What Is Cross-Domain Access?

Domains are organized around business systems or processes. Very few domains can function without data from other domains. APIs are the preferred method of accessing data from another domain. Direct database access should only be used in exceptional circumstances and with approval of all parties involved.  

## UAPI Spec And The BYUAPI

The BYUAPI is the implementation of the UAPI specification using BYU specific resources including Students, Employees, Persons, etc. The BYUAPI is implemented as a [facade](https://en.wikipedia.org/wiki/Facade_pattern) on the top of domain APIs. The UAPI specification allows consumers to conform to a single JSON and URL format and function when interacting with all compliant APIs. Domain APIs that are UAPI specification compliant are not necessarily part of the BYUAPI. 

## Implications of varying from the specification

In general domains externally consumable APIs Domain should conform to as much of the UAPI specification as possible. 

### Coupling Between Domains

![Figure 1](./images/direct-domain-access.jpeg "Figure 1")

Consider the above example. Domain A provides services to domains B, C, D, E, and F and consumes services from domains G and H. If domain A chooses to define a unique API specification domains B, C, D, E, and F must implement a translation layer (ACL) for any interaction with A. A must also implement a translation layer for any interaction with G and H for the same reason. Any changes A needs to make to the services it provides must be coordinated with all consuming domains (tight coupling). A must also respond to any changes G or H make to the services it is consuming.    

![Figure 2](./images/UAPI-access.jpeg "Figure 2")

Now consider how the relationships change if all domains conform to the UAPI specification for all cross-domain APIs. In this example some of the APIs provided by A are used as part of the BYUAPI implementation. For those APIs domains B, C, and D can access their needed APIs via the BYUAPI reducing the coupling with A to a single relationship with the BYUAPI. The APIs A is consuming from domain G is also part of the BYUAPI and again the coupling for G is reduced to only the relationship with the BYUAPI. Domains E, F, and H are not part of the BYUAPI. While the relationship between domains remains because they are UAPI specification compliant the need for a translation layer is eliminated.  

### BYUAPI impacts

They BYUAPI owns no domain data. BYUAPI provided resources are aggregations of data from a number of underlying domains. The closer the APIs from the underlying domains follow the UAPI specification the less work the BYUAPI facade layer has to do. If the domain API is fully compliant the API Manager may be the only part of the facade necessary. 

## Implementation considerations

### URL Namespaces
The URL namespace designated for Domain APIs is:

```
https://api.byu.edu/domains/{domain_name}
```

The *domain_name* portion of the Domain API URL namespace is determined by the domain team. For example, if a domain team had chosen the domain_name "identity," its namespace would be `https://api.byu.edu/domains/identity`. All domain APIs would reside under that URL within the API Manager. All UAPI spec compliant APIs that are designated as part of the BYUAPI are under the `https://api.byu.edu/byuapi` URL namespace. 

### API Management

Domain APIs provided for use by other domains and the BYUAPI are required to be managed by our API Manager. Intra-domain APIs that are exclusively for use within a domain can be managed by the API Manager but are not required to be. If the domain team chooses to not use the API Manager they still should address authorization, authentication, and monitoring.