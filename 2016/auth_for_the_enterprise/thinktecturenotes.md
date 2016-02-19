

Securing Web Applications + Web Services (APIs)
Asmx, WCF -- securing services different from web ui, (forms auth vs. basic auth)
OpenID + Oauth2 lets you secure Web Applications and APIs the same way

Application scenarios (what problem are we solving)

Web App -> APIs
Browser -> Web App
Browser -> Web APIs
Native App -> Web APIs
Server App -> Web APIs

STS = Security Token Service
Authentication as a Service

OAuth2 is not an authentication protocol

Facebook, google, github implemented their own authentication protocol on top of oauth2
but it was not standard between the three and thus required their own client 
libraries to use

Enter OpenID Connect 1.0 -- a simple identity layer on top of the
oauth 2.0 protocol

Authentication protocol on top of oauth2
- defines identity tokens (additional to oauth2's access tokens)
- defines a standard token type
- defines standard cryptography
- defines validation procedures for tokens
- combines authentication with short/long-lived delegated api access
- defines flows for native, browser, server auth scenarios
- exposes metadata endpoint to allow applications to configure themselves via the metadata endpoint

implementations in all languages

openid connect pieces:
- authorize endpoint (interacts with humans, renders uis)
- token endpoint (machine communication, web api)
- userinfo endpoint (profile endpoint, can use JWT and send everything in the token, or use reference token to retrieve claims from userinfo endpoint)

Flows (part of oauth)
- implicit
    - native/browser/web applications
- auth code flows
    - server-based applications
    - client authentication
- hybrid flows
    - combines both
    - shortcuts for certain scenarios
    - applications with client and server parts
    
- implicit flow

    GET /authorize
    
    ?client_id=app1
    &redirect_uri=https://app.com/cb
    &response_type=id_token
    &response_mode=form_post
    &scope=openid email
    
(standard set of scopes defined by openid connect protocol)

Consent

(your user id, personal info are "resources")
Enterprise applications won't show consent (who cares, the enterprise owns your identity)
Customer facing would show consent

Response does a form postback with the token

Parts of a token

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

State
nonce 

used to mitigate XSS attacks
state = random number you send to provider
provider redirects with the same random number you sent

nonce is similar, but nonce goes into identity token

demo using handwritten openid connect client

show meta data url

issuer
endpoints
supported scopes
jwks_uri (json web key set)
- shows public key that is used to validate tokens
- so you can download the key from that token so you dont have to configure the cert on
each server, allows client to auto-update certificates to validate tokens

demo of "katana" -- openidconnectauthenticaiton

providers only issue tokens to clients they know about (you must register your client)

how do you trigger the challenge? 
Generate a 401 using [Authorize] attribute

implicit flow is designed for client applications (javascript/SPA, mobile, not server)

identical to auth code flow, but you dont pass client secret

application doesnt need to know anything about authentication logic, all handled
via middleware / configuration

id_token token (combine access token and identity token -- shows dual consent)
oauth2 does not dictate token type -- identitymodel supports JWT or reference token
(ref token is an id that gets echod to auth server to get the token)

what about token expirations:

if the token is expired, you can go back to the auth endpoint and if the cookie is still
valid, you will get a new token
if the auth cookie is expired, you will have to ask your user to re-authenticate

google uses short lived access token (1 hour), but long lived auth session (14 days)

authorization code:
long lived api access
https://sts.com/authorize?client_id=
&scope=openid email apioffline_access
&redirect_uri=https://app.com/resources
&response_type=code

server exchanges code, client, secret for token
token contains access_token and refresh_token
access token is valid for 1 hour, refresh token is valid for 1 year
refresh token is exchanged for updated access tokens

revoke token -> then refresh fails

hybrid flow
&scope=openid email api1 api2 offline_access
&response_type=code id_token token

openid session management
client-initiated logout

clearing cookie does not log you out of sts
openid connect has a session endpoint to allow logout
can use openid to also enable "single sign out" to logout of all applications

client-initiated logout

GET /endsession
?id_token_hint=a72...b12
&post_redirect_uri=https://app.com

different implementations