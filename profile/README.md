# beffroi-php

An identity provider for the Symfony ecosystem: an OAuth 2.0 and OpenID Connect authorization server a team installs in its own application, in its own language, and keeps.

On the map of a Symfony application, the interface is Symfony, the API is API Platform, and the identity provider is always something else: Keycloak, Auth0, Entra. It is the one box the ecosystem does not write in its own language. That box is what we are writing. The code goes by *Heidi* until the product has its name.

## What lives here

| Package | What it is |
|---|---|
| `heidi/core` | The identity core: tenants, users, credentials, linked identities, groups, authentication sessions, signing and encryption keys. Pure PHP, no framework. |
| `heidi/oidc` | OAuth 2.0 and OpenID Connect: clients, authorization requests, grants, tokens, discovery, JWKS, UserInfo, logout. Pure PHP. |
| `heidi/symfony-bundle` | The Symfony integration: configuration, routes and controllers, security, persistence, the login and consent pages. |
| The standalone application | A skeleton and the bundle, which is the recommended way to deploy the provider. |

All of it is MIT. What is not open is what *operates* instances rather than what an instance serves: the control plane, with its tenants, master keys, retention, export and observability. A protocol feature is never what is held back, and never will be: SAML and SCIM belong to the open packages exactly as DPoP and the device flow do.

## What we hold ourselves to

**Secure by default, with no way to do it wrong.** PKCE `S256` required, no implicit flow, no password grant, exact redirect URI match, an `iss` in every authorization response, `at+jwt` access tokens a resource server cannot mistake for another kind of token. There is no `insecure_*` option to copy from a tutorial: where an exception is unavoidable it is named, carried by one client, and bounded.

**Conformance is proved by someone else.** The OpenID Foundation conformance suite runs in continuous integration, plan by plan, on every change. What is green is what a run says is green, on a date, with the run to open.

**Your users stay yours.** The provider owns its identity model instead of hooking into your `User` entity, so what applies to your users can be read, replaced, and taken elsewhere with them.

**A limit is fixed where it lives.** When a client library, a framework or a specification implementation is what stands in the way, the fix is proposed upstream rather than worked around here.

## Where things stand

The provider runs, it signs in users with passwords, passkeys and one-time codes, and it answers the OpenID Connect certification plans it aims at. It is not released: the repositories are private while the work is judged plan by plan, and they open under MIT with the first release.

## The name

A belfry is the tower a town built for itself. It held the bells that told everyone what was happening, and the chest where the town kept its charter. It watched over the town without owning it, which is the job we want for a piece of software that stands at the door.
