# An OpenID Connect provider that installs like a Symfony bundle

Your application already has users, a database, a login page and a deployment. What it does not have is the piece that speaks OAuth 2.0 and OpenID Connect to everything else, and the usual answer is a second stack to run: Keycloak, Auth0, Entra. This is that piece, in PHP, deployed with your application, with your users in your database and your login page in Twig.

## What it looks like

Declare a tenant, say where the keys are sealed, register a client:

```yaml
heidi:
    tenant:
        issuer: 'https://id.example.com'
    keys:
        kms: op                     # a KMS client of the framework configuration
        key_id: 'heidi-{tenant}'
    clients:
        my-app:
            secret: '%env(MY_APP_SECRET)%'
            redirect_uris: ['https://app.example.com/login/check']
```

The application then serves a provider any OpenID Connect client can discover and use:

```
/.well-known/openid-configuration   /jwks   /authorize   /token   /userinfo
/introspect   /revoke   /par   /login   /consent   /logout
```

The pages are Twig templates you override. The rest is services you can replace.

## What is in the box

| | |
|---|---|
| **Sign-in** | Passkeys as a first factor (WebAuthn, FIDO metadata verified), passwords with typed hashes, TOTP with backup codes, attempts limited per user, step-up decided per user, `acr` and `amr` that say what was actually proved |
| **Tokens** | Authorization code with PKCE `S256`, `at+jwt` or opaque per client, refresh token rotation, revocation, introspection, introspection responses signed for a resource server that verifies them |
| **FAPI 2** | Pushed authorization requests, signed request objects, signed authorization responses (JARM), DPoP, mTLS client authentication and certificate-bound tokens, ID token and UserInfo encrypted to the client, `PS256`, `ES256` and `EdDSA` |
| **Logout** | RP-initiated, front-channel, back-channel with retries, and a session that ends for every client at once |
| **Keys** | One key per tenant, sealed by a KMS (sodium, a key file, Vault Transit, AWS KMS), rotation with no downtime and no client left behind, a JWK Set carrying the signing and the encryption keys |
| **In your project** | Doctrine entities and migrations, console commands (`heidi:keys:rotate`, `heidi:user:create`, `heidi:keys:list --check`), a Symfony profiler panel that shows what came in, what was decided and what went out |

## Why this rather than a second stack

- **One deployment, one language.** The provider runs in the application you already deploy, debugged with the tools you already use, on the PHP version you already run.
- **Your users stay yours.** The provider owns its identity model instead of hooking into your `User` entity, so nothing of your application is bent to fit it, and nothing holds your users hostage.
- **No way to do it wrong.** PKCE `S256` required, no implicit flow, no password grant, exact redirect URI match, an `iss` in every authorization response. There is no `insecure_*` option to copy from a tutorial; where an exception is unavoidable it is named, carried by one client, and bounded.

If what you need is a directory, a federation hub and an administration console for the whole company, Keycloak is a good answer. If what you need is your application to be its own identity provider, this is that.

## How we know it works

The OpenID Foundation conformance suite runs in continuous integration, plan by plan, on every change: Basic OP, Config OP, Form Post Basic OP, RP-Initiated Logout, Front-Channel and Back-Channel Logout are green, and the two FAPI 2 plans run in their four variants. Symfony's own `oidc_login` client consumes the provider without a patch, and every limit found on the way is proposed upstream rather than worked around here.

Green is what a run says on a date, with the run to open. The certification itself, the mark and the listing, is a separate step and it has not been taken.

## Where things stand

The provider runs and is judged, plan by plan. It is not released: the repositories are private while that work is finished, and they open under MIT with the first release. What is being built next is the other grants, dynamic client registration, SAML 2.0, SCIM and federation.

What will never be held back is a protocol feature: SAML and SCIM belong to the open packages exactly as DPoP and the device flow do. What is not open is what *operates* instances rather than what an instance serves: the control plane, with its tenants, master keys, retention, export and observability.

## The name

A belfry is the tower a town built for itself: the bells that said what was happening, and the chest where the charter was kept. It watched over the town without owning it, which is the job of a piece of software standing at the door.
