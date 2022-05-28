---
title: "OAuth 2.0 and OpenID Connect: Which flow should you use?"
excerpt: "A brief introduction to OAuth 2.0 and OpenID Connect that allows you to quickly choose the best flow for your application."
date: 2020-03-08
last_modified_at: 2020-03-08
preview:
  image: /assets/images/2020-03-08-oauth2-oidc-which-flow/oauth-logo.png
categories:
  - Quickstart
tags:
  - Software Architecture
  - OAuth
  - OpenID Connect
---

When designing a software system that needs to deal with [federated identities](https://en.wikipedia.org/wiki/Federated_identity), OAuth 2.0 and OpenID Connect are some of the best options at your disposal.

In this quickstart guide, I'm going to explain what OAuth 2.0 and OpenID Connect are about. Then I will guide you into choosing the right authentication/authorization flow for your applications.

## OAuth 2.0

OAuth 2.0 is an open _framework_ that allows to securely obtain __authorization__ to access resources via HTTP.

For example, you can tell Google that Trello is allowed to access your Google Calendar in order to associate tasks with dates, _without sharing your Google password with Trello_. This is possible because Trello can send OAuth 2.0 tokens to Google which prove your consent.

OAuth 2.0 is not considered to be a _protocol_ because the [specification](https://tools.ietf.org/html/rfc6749) is rather vague. In fact, this is one of the reasons why Eran Hammer [resigned](https://hueniverse.com/oauth-2-0-and-the-road-to-hell-8eec45921529) from his role of lead author for OAuth 2.0.

In the following sections we will go through the main concepts of the OAuth 2.0 framework.

### Purpose

One of the best analogies is to consider an OAuth 2.0 token to be the __valet key__ to your car. By using the valet key, the valet can start and move (for a limited distance) your car but he can't access the trunk.

An OAuth 2.0 token serves the same purpose of the valet key. As a resource owner, you decide which data each consumer is allowed to access from your service providers. You can give each consumer a different valet key, while being sure that none of these consumers has access to your full key or to any data from which they can build a full key.

### Roles

Roles identify the _actors_ that can participate in OAuth 2.0 flows. There are 4 roles:

- **Resource Owner**: the user who is giving authorization to access his account information.
- **Client**: the application that attempts to get access to the user's account. It needs the user's permission in order to get this access.
- **Authorization Server**: the server that allows users to approve or deny an authorization request.
- **Resource Server**: the server that consumes the user's information.

This is how OAuth 2.0 roles would be translated in our example:

![OAuth 2.0 roles example](/assets/images/2020-03-08-oauth2-oidc-which-flow/oauth2-roles.svg){: class="center"}

### Endpoints

Endpoints are URIs that define the location of authorization services. In OAuth 2.0, there are 3 endpoints:

- The **Authorization endpoint** is on the authorization server. It's used for the resource owner's log in and grants authorization to the client application.
- The **Redirect endpoint** is on the client application. The resource owner is redirected to this endpoint after having granted authorization at the authorization endpoint.
- The **Token endpoint** is on the authorization server. This is where the client application exchanges some secret information for an access token.

This is how the OAuth 2.0 endpoints would be translated in our Trello example:

![OAuth 2.0 endpoints example](/assets/images/2020-03-08-oauth2-oidc-which-flow/oauth2-endpoints.svg){: class="center"}

### Scopes

Scopes can be used to limit a client's access to the user's resources. Basically:

1. A client can request one or more scopes.
2. The requested scopes are presented to the user in the consent screen.
3. If the user gives his consent, the access token issued to the application will be limited to those scopes.

The OAuth 2.0 specification does not define any specific values for scopes, since they are highly dependent on the service provider. For example, these are the scopes for the Google Calendar's API:

![OAuth 2.0 Google Calendar scopes](/assets/images/2020-03-08-oauth2-oidc-which-flow/oauth2-gcalendar-scopes.png){: class="center"}

### Grants

Authorization grants are methods for a client to get an __access token__. The access token proves that a client has the permission to access the user's data.

Authorization grants are also known as _flows_.

OAuth 2.0 recommends to use one of the following grants:

- [Authorization code](https://marcolabarile.me/notes/oauth2-and-openid-connect/oauth2/#authorization-code-grant), designed for clients which can securely store secrets. This grant is typically used when the client is a web server.
- [Authorization code with PKCE](https://marcolabarile.me/notes/oauth2-and-openid-connect/oauth2/#proof-key-for-code-exchange-pkce), extends the Authorization code grant with additional security measures. It's typically used when the client is a mobile or a web browser application.
- [Client credentials ](https://marcolabarile.me/notes/oauth2-and-openid-connect/oauth2/#client-credentials-grant), used when clients need to be authorized to act on behalf of a *service account* rather than a human user.
- [Device authorization](https://marcolabarile.me/notes/oauth2-and-openid-connect/oauth2/#device-authorization-grant), designed for clients with limited input capabilities (eg. AppleTV apps).
- [Refresh token](https://marcolabarile.me/notes/oauth2-and-openid-connect/oauth2/#refresh-token-grant), used to refresh access tokens.

But there are also some grants whose usage is discouraged by the [OAuth 2.0 Security Best Current Practice document](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-13):

- [Implicit grant](https://marcolabarile.me/notes/oauth2-and-openid-connect/oauth2/#implicit-grant), it **was** the the recommended choice for native and web browser applications.
- [Password grant](https://marcolabarile.me/notes/oauth2-and-openid-connect/oauth2/#password-grant), exchanges the user's clear-text credentials for an access token.

### Which grant should you use?

Now that you have an idea about the usage and purpose of OAuth 2.0, you are asking yourself which grant is best match for your application. Basically, the choice boils down to the type of client and user:

![OAuth 2.0 which grant should you use?](/assets/images/2020-03-08-oauth2-oidc-which-flow/oauth2-which-grant-to-use.svg){: class="center"}

### Learn more

If you want to learn more about OAuth 2.0, you can consult these resources:

- [My notes on OAuth 2.0](https://marcolabarile.me/notes/oauth2-and-openid-connect/oauth2/)
- [Aaron Parecki's blog](https://aaronparecki.com/oauth-2-simplified)
- [OAuth 2.0 Simplified, by Aaron Parecki](https://oauth2simplified.com/)
- [OAuth.net Playground](https://www.oauth.com/playground/)
- [Getting Started with OAuth 2.0, on Pluralsight](https://app.pluralsight.com/library/courses/oauth-2-getting-started/table-of-contents)

## OpenID Connect

OpenID Connect (*OIDC*) is an authentication protocol. It builds upon [OAuth 2.0](#oauth-20) and [JSON Web Tokens](https://marcolabarile.me/notes/oauth2-and-openid-connect/jwt/) to provide users one login for multiple sites.

For example, Trello lets you create a new account using an existing Google account. In this case, Trello only needs to access some basic information of your Google account (e.g. email and username). The main advantage is that *you don't need to share your Google password with Trello*. This is possible because Trello can use OpenID Connect tokens to validate your Google account identity.

In the following sections we will go through the main concepts of the OpenID Connect protocol.

### Purpose

Sometimes you need only an authentication mechanism, without any authorization logic. But OAuth 2.0 is not good enough by itself to be used for authentication. Let's see why by examining how authentication could be implemented with OAuth 2.0:

We could use a custom OAuth 2.0 scope named *signin* that represents the permission to access the basic information of the user. The idea is: _if the user is able to grant us that token, then surely he must have authenticated_. But there are some problems with this approach:

- The authentication step made by the user is not correlated to the authorization request.
- The user data endpoint is usually different for each service (e.g. Google may have a different endpoint from Facebook).

In fact, this usage of OAuth 2.0 is vulnerable to a very simple yet dangerous impersonification attack: if a malicious application has been granted access to your user data, it can reuse the same *access_token* to authenticate as you to another service (e.g. e-banking) using the same authentication mechanism.

With this approach, providers using OAuth 2.0 for authentication would need to implement extra security measures. But this measure may not be standardized across different providers! This means software developers would need to write different code to authenticate to different providers.

So, in order to make OAuth 2.0 suitable for authentication we need:

- A new token type which can be validated in a standardized way.
- A standardized mechanism to access the user's information.

This is exactly what OpenID Connect provides on top of OAuth 2.0, with the help of JWT.

### Endpoints

Endpoints are URIs that define the location of authentication services. In OpenID Connect, there are 3 endpoints:

- The **Authorization endpoint** is usually on the authorization server. It's used to perform authentication of the user.
- The **Token endpoint** is usually on the authorization server. This is where the client application exchanges some secret information for an access token, [ID token](#id-tokens) and optionally a refresh token.
- The **UserInfo endpoint** is usually on the authorization server. It's used to retrieve claims about the currently authenticated user.

This is how the OpenID Connect endpoints would be translated in our Trello example:

![OpenID Connect endpoints example](/assets/images/2020-03-08-oauth2-oidc-which-flow/oidc-endpoints.svg){: class="center"}

### Scopes

OAuth 2.0 scopes in OpenID Connect are used to define to which [ID token](#id-tokens) claims the client is requesting access.

The OpenID Connect specification defines a set of [standard scopes](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). The only required scope is *openid*, which states that the client intends to use the OpenId Connect protocol.

### Id Tokens

ID Tokens are JWTs introduced by OpenID Connect that contain identity data. An ID Token can be returned in a token response, alongside an *access_token*. It can be consumed by the application and it also contains user information (e.g. username, email).

The OpenID Connect specification defines a set of [standard claims](https://openid.net/specs/openid-connect-core-1_0.html#StandardClaims). Still, it's possible to define custom claims and add them to your tokens.

### Flows

The OpenID Connect specification defines 3 authentication flows:

- [Authorization code flow](https://marcolabarile.me/notes/oauth2-and-openid-connect/openid-connect/#authorization-code-flow), designed for clients which can securely store secrets. This grant is typically used when the client is a web server.
- [Implicit flow](https://marcolabarile.me/notes/oauth2-and-openid-connect/openid-connect/#implicit-flow), designed to immediately return an access token to the client. It's typically used for native and web browser applications.
- [Hybrid flow](https://marcolabarile.me/notes/oauth2-and-openid-connect/openid-connect/#hybrid-flow), combines the mechanisms of the Authorization code flow and Implicit flow. It allows the front end and back end of the application to receive their own tokens, with their own scopes.

### Which flow should you use?

Now that you have an idea about the usage and purpose of OpenID Connect, you are asking yourself which flow is best match for your application. Basically, the choice boils down to the type of client:

![OpenId Connect which flow to use](/assets/images/2020-03-08-oauth2-oidc-which-flow/oidc-which-flow-to-use.svg){: class="center"}

### Learn more

If you want to learn more about OpenID Connect, you can consult these resources:

- [My notes on OpenID Connect](https://marcolabarile.me/notes/oauth2-and-openid-connect/openid-connect/)
- [OpenID Foundation's website](https://openid.net/)
- [Comprehensive list of OpenID Connect specifications](https://openid.net/developers/specs/)