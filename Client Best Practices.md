# Client Best Practices

This document lays out best practices for interacting with the University APIs.

**What is a Client?** A client is any computer that makes requests an API. That could be a browser making AJAX requests or a back-end server.

## Status Codes

The list below highlights some status codes you will need to know to effectively use the UAPI.

### 200

Although a 200 level code generally indicates success, within the UAPI it is possible to request multiple sets of attributes with a single request. Some of those attributes may come back, but for some you may not be authorized, or there may be some other error. For that reason, when using the UAPI a `200` response code only indicates that you received a UAPI response.

### 401

### 403

This status code will be provided by WSO2 and indicates authentication was provided but that the user is denied access to the resource. This can indicate one of several things:

1. The access token has expired.
2. The access token has been revoked.
3. The access token is incorrect.

## Language Specific Client Tools

## Token Handling

## Batch Requests

Traditionally batch requests (requests asking for thousands of records of data) have been used to get a local copy of data from the official source. These types of requests can typically take several hours are discouraged in favor of [event-subscriptions](#event-subscriptions). With [event-subscriptions](#event-subscriptions) both clients and servers do less work and are more up to date.

## Event Subscriptions

## User Identity

### Authentication

BYU uses Central Authentication Service (CAS) for establishing user sessions and user identity. CAS protects its cookies so that they are unreadable by either the browser or other servers. [To obtain user identity from CAS you will need to get a CAS token.](#obtain-the-users-cas-identity)

Note that CAS at BYU currently works as a single sign in, meaning that the user only needs to sign in to CAS once and all other servers that use CAS will get the same identity for the user. To prevent redirecting to CAS for every request, most clients will store their own session once identity has been established via CAS. This can lead to stale sessions where the client believes that the user is still signed into CAS after they have signed out. To mitigate this inconsistency it may be wise to recheck the CAS identity on a regular basis (for example, every 10 minutes).

### Authorization

For authorization, BYU uses Web Service Oxygen (WSO2). The use of this service enables clients to make web service requests to the University API.

### CRITICAL INFORMATION

WSO2 uses CAS to establish user identity and it uses it's own session to keep that identity. When getting OAuth tokens the WSO2 session may conflict with that of WSO2. The two possible conflicts are: 

1. The user logged in to CAS may not be the same user as is logged into WSO2. In this situation any OAuth tokens retrieved will be for the wrong user. Essentially you will be giving Alice's access to Bob. Bob will ask for his personal information and instead get Alice's.

2. The user may not be logged into CAS when WSO2 believes they are. Under this scenario OAuth tokens can still be retrieved after a user has logged out of CAS. In other words, an anonymous user will have access to Bob's data.

It is possible to avoid both of these problem scenarios by restablishing the CAS identity and matching it to the WSO2 identity prior to getting OAuth tokens. If the identies do not match then CAS and WSO2 sessions should be killed manually and then re-established. Read on for details.

### Obtain the User's CAS Identity

1. Direct the browser client to BYU's CAS URL. You will want to provide a `SERVICE_URL` that CAS will redirect the browser back to after authentication. When the browser is redirect to the `SERVICE_URL` it will also have an additional query parameter named `token` if the user is logged in.

    1. If you want the user to see the login screen (if they are not logged in) then direct the browser to: https://cas.byu.edu/login?service=SERVICE_URL. If the user is already logged in then they will not see the log in screen and instead will quickly be redirected to the `SERVICE_URL`.

    2. If you want to see if the user is logged in without showing the log in screen then redirect the browser to: https://cas.byu.edu/login?gateway=true&service=SERVICE_URL

2. The browser will redirect to the `SERVICE_URL` provided. 

    If a query parameter named `token` is present then the browser is indicating that the user is logged into CAS and you can proceed to the next step. 
    
    If no token is provided then the user is not logged in to CAS and you may want to end any sessions you have established for the user.

3. If your client (server) application has received the `token` then it should now make an HTTPS request directly to CAS, using the `token`. This step is used to validate that the `token` is authentic and provided by CAS and also is used for retrieving the user's identify. The browser should not be involved in this step.

    Have your client (server) make an HTTPS request to https://cas.byu.edu/proxyValidate?service=SERVICE_URL&token=TOKEN. The `SERVICE_URL` must be exactly the same as what was used in Step 1. The `TOKEN` is the `token` query parameter that CAS sent to your client (server).

4. TODO: how to get identity from the response.

### Obtain the User's WSO2 Identity

# Notes

---

- You should not be using personal credentials in production. Your WSO2 app owner should be a non-person
- Some client applications should be designated as "University Internal" and they should be auto authorized to access resources.

### BYUAPI

- Should use the API spec
- Anyone may subscribe (no governance) - but you must have an active role on campus
- No DSA
- Found in the dev portal by default
- Should not contain highly confidential sensitive attributes (SSN, payroll info, etc)
- No access w/o subscription
- We use OAuth. No API keys. No more "Acting-For" header.
- Uses JWT's (original-jwt and current jwt)

### Domain API

- Only the domain team may subscribe. Others may subscribe by permission. Governance by domain team.
- Can be found in the dev portal with effort
- Generally use of these outside of the team should be discouraged

### BYUAPI Principles

1. No anonymous (unauthorized) access. Client application may be resource owner.
2. We use Oauth. Retire API key. (Figure out how to 'Acting For')
3. Client applications must subscribe.
4. Self service requires not additional governance.
5. Request a self-service web page is not the same as request a self-service API
6. Authorize clients to self-service
