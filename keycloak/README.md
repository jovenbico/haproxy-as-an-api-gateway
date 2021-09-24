# What is keycloak?

> Keycloak is a high performance Java-based identity and access management solution. It lets developers add an authentication layer to their applications with minimum effort.

[Overview of keycloak](https://www.keycloak.org)
[Keycloack image registry](https://quay.io/repository/keycloak/keycloak)

# TL;DR

```console
$ docker run -d --name keycloak \
  --env KEYCLOAK_USER=admin \
  --env KEYCLOAK_PASSWORD=admin \
  --env DB_VENDOR=h2 \
  -p 8080:8080 \
  quay.io/keycloak/keycloak:15.0.2
```

```console
curl --location --request POST 'http://keycloak.homelab.com/auth/realms/site-services/protocol/openid-connect/token' \
--header 'Authorization: Basic bXlfYXBwOjU1ZWJlZTAwLWEyODctNDM5Mi05YzJlLTQ0NDYxZjk4YmU2OQ==' \
--data 'grant_type=client_credentials'
```