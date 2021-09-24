# What is keycloak?

> Keycloak is a high performance Java-based identity and access management solution. It lets developers add an authentication layer to their applications with minimum effort.

[Overview of keycloak](https://www.keycloak.org)

[Keycloack image registry](https://quay.io/repository/keycloak/keycloak)

## TL;DR

```console
$ docker run -d --name keycloak \
  --env KEYCLOAK_USER=admin \
  --env KEYCLOAK_PASSWORD=admin \
  --env DB_VENDOR=h2 \
  -p 8080:8080 \
  quay.io/keycloak/keycloak:15.0.2
```

## Set up the Project

go to Keycloak Administration Console with the username and password `admin`.

- Create new realms for each service
- Click the Client Scopes link next. Add three scopes—bronze, silver, and gold—which will serve as different pricing tiers
- Click the Create button on the Clients screen to add a new client.  

After you’ve created the client, you’re taken to the client’s Settings screen. Because we want to enable machine-to-machine authentication, you must enable the OAuth 2 Client Credentials grant. That’s what OAuth calls the workflow for allowing an application to request an access token. To enable this on the Settings screen, change the `Access Type` field to `confidential` and set `Service Accounts Enabled` to on.

- on the Client Scopes tab, add the bronze scope. Remove all of the other previously assigned client scopes.
- go to the Mappers tab and create a new mapper. Set its Mapper Type field to Audience and its Included Custom Audience field to the URI of your API service. example `http://site1.homelab.com`

## Get an Access Token

```console
curl --location --request POST 'http://keycloak.homelab.com/auth/realms/site-services/protocol/openid-connect/token' \
--header 'Authorization: Basic bXlfYXBwOjU1ZWJlZTAwLWEyODctNDM5Mi05YzJlLTQ0NDYxZjk4YmU2OQ==' \
--data 'grant_type=client_credentials'
```

;;;
{
  "alg": "RS256",
  "typ": "JWT",
  "kid": "k89QPaKowNRrI1YtRYGRNLntjvr8i64EP-Xn9tV93Mw"
}
;;;

;;;
{
  "exp": 1632468367,
  "iat": 1632468067,
  "jti": "e6e799bf-ba78-48ff-af0a-0d52fdf1d5bb",
  "iss": "http://keycloak.homelab.com/auth/realms/site-services",
  "aud": "http://site1.homelab.com",
  "sub": "18ac156d-84de-49cb-9fec-d227f7db1c92",
  "typ": "Bearer",
  "azp": "my_app",
  "acr": "1",
  "scope": "bronze",
  "clientId": "my_app",
  "clientHost": "192.168.2.23",
  "clientAddress": "192.168.2.23"
}
;;;