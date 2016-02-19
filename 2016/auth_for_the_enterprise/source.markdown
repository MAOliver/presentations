class: center, middle
# OAuth for the Enterprise 

.right[Matthew Oliver, Solution Architect]

---
class: middle

# Overview

1. Authentication / Authorization
2. Enterprise Auth Scenarios
3. OAuth2
4. OpenID Connect
5. Tokens and Flows

---
class: middle

# Definitions

## Authentication

> Authentication is any process by which you verify that someone is who they claim they are.
-- [Apache httpd docs](http://httpd.apache.org/docs/current/howto/auth.html)

## Authorization

> Authorization is any process by which someone is allowed to be where they want to go, or to have information that they want to have.
-- [Apache httpd docs](http://httpd.apache.org/docs/current/howto/auth.html)

---
class: middle

# Traditional Enterprise Auth

.center[![Markdown document with corresponding PDF](images/diag_manyauth.png)]

???

- Auth logic implemented multiple times in multiple ways
- no central hub
- no consistent way of doing things
- harder to audit
- one type of auth for internal users and another for external
- one type of auth for apis and one for web applications or native
- the identity provider is also responsible for modeling permissions or roles

---
class: middle

# Auth using a Security Token Service

.center[![Markdown document with corresponding PDF](images/diag_sso.png)]

???

- Separates Authentication and Authorization by splitting up identity and claims
- "A claim is a statement that one subject, such as a person or organization, makes about itself or another subject."
- "Claims are not what the subject can and cannot do. They are what the subject is or is not." 
- Examples of claims "role, given_name, username, first_name, email, etc."
- The authority verifies that the user is who they say they are and the STS issues tokens which contain facts (claims) about the user
- Auth can be implemented the same way for apis, web applications, mobile, etc.

---
class: middle

# Using OAuth2 for User Authentication

.center[![OAuth is not an authentication protocol](images\2016-02-19 05_42_24-C__wrk_github_maoliver_presentations_2016_auth_for_the_enterprise_thinktectureno.png)]

???

- lack of standards to authenticate users
- hacky implementations from google, facebook, etc.
- security holes
- security holes and custom clients for each implementations

---
class: middle

# Using OpenID Connect 1.0 for Authentication

Authentication protocol on top of oauth2
- defines identity tokens (additional to oauth2's access tokens)
- defines a standard token type
- defines standard cryptography
- defines validation procedures for tokens
- combines authentication with short/long-lived delegated api access
- defines flows for native, browser, server auth scenarios
- exposes metadata endpoint to allow applications to configure themselves via the metadata endpoint

???

- /.well-known/openid-configuration/


openid connect pieces:
- authorize endpoint (interacts with humans, renders uis)
- token endpoint (machine communication, web api)
- userinfo endpoint (profile endpoint)

- can use JWT and send everything in the token, or (identityserver) use reference token to retrieve claims from userinfo endpoint

---
class: middle

# What's in a token?

    header

        - "typ" - type
        - "alg" - algorithm you use to validate the token signature 

    claims

        - "iss" - issuer of the token
        - "exp" - expiration (epoch time)
        - "aud" - audience -- the client_id so you can verify that the token is for you
        - "sub" - unique id of the user - never be re-used and is immutable

    signature

        (x509 cert thumbprint that was used to create signature)

???

other pieces of information exchanged include:
- State
- nonce 

used to mitigate XSS attacks
- state = random number you send to provider
- provider redirects with the same random number you sent

- nonce is similar, but nonce goes into identity token        

---
class: middle

# Authentication Flows with OpenID Connect

.center[![OpenID Connect Auth Flow](images/ascii_auth_flow.png)]

???

The OpenID Connect protocol, in abstract, follows the following steps:

1. The RP (Client) sends a request to the OpenID Provider (OP).
2. The OP authenticates the End-User and obtains authorization.
3. The OP responds with an ID Token and usually an Access Token.
4. The RP can send a request with the Access Token to the UserInfo Endpoint.
5. The UserInfo Endpoint returns Claims about the End-User.

RP = Relying Party
OP = OpenID Provider    

some other important parts of this interaction:

- Scopes - OAuth2 construct used to model the resource (identity or api access)
- Client - the application requesting access to the resource

---
class: middle

# Brief Overview of Authentication Flows and Grant Types

* Grants
    - Authorization Code Grant
    - Implicit Grant
    - Resource Owner Password Credentials Grant
    - Client Credentials Grant
* Authentication Flows
    - Authorization Code Flow (server-based web application / client to client)
    - Implicit Flow (SPA / native / browser-based web applications)
    - Hybrid Flow (applications with characteristics of both, provides shortcuts)

???

- Grants are defined as a part of oauth
- Authentication Flows are built on top of the grants using response types
  to determine the "flow"

---
class: middle

# Authorization Code Flow Step 1

.center[![Auth Code Request](images\auth_code_request.png)]

---
class: middle

# Authorization Code Flow Step 2

.center[![Auth Code Response](images\auth_code_success_response.png)]

---
class: middle

# Authorization Code Flow Step 3

.center[![Auth Code Token Request](images\auth_code_token_request.png)]

---
class: middle

# Authorization Code Flow Step 4

.center[![Auth Code Token Response](images\auth_code_token_response.png)]

---
class: middle

# Implicit Flow Request

.center[![Auth Code Response](images\implicit_flow_request.png)]

???

- implicit vs. auth code flow
- fewer steps, more restrictive in scope
- authorization code flow allows for offline_access scope which
  allows for long-running server processes to refresh token without
  use of an auth cookie

---
class: middle

# What determines the flow?

> | "response_type" value | Flow                        |
> | --------------------- | --------------------------- |
> | code 	              | Authorization Code Flow     |
> | id_token 	          | Implicit Flow               |
> | id_token token        | Implicit Flow               |
> | code id_token 	      | Hybrid Flow                 |
> | code token 	          | Hybrid Flow                 |
> | code id_token token   | Hybrid Flow                 |

???

The flow used is determined by the response_type value contained in the 
Authorization Request.

---
class: middle

# Bibliography

> "Authentication and Authorization." Apache.org. 
>> [http://httpd.apache.org](http://httpd.apache.org/docs/current/howto/auth.html), 2016. Web. 01 February 2016

> "OAuth." Wikipedia.org
>> [https://en.wikipedia.org/wiki/OAuth], 2016. Web 02 February 2016

> "User Authentication with OAuth 2.0." OAuth.net. Justin Richer.
>> [http://oauth.net/articles/authentication/], 2016. Web 02 February 2016

> "OpenID Connect Core 1.0 incorporating errata set 1." OpenID.net. Nat Sakimura, et al.
>> [https://openid.net/specs/openid-connect-core-1_0.html], 2014. Web 02 February 2016

---
class: middle

# Bibliography

> "Introduction to OpenID Connect, OAuth2, and IdentityServer." Dominick Baier.
>> [https://vimeo.com/113604459], December 2014. Web 02 February 2016

---
class: middle, center

# Questions / Demo

Questions before demo?
