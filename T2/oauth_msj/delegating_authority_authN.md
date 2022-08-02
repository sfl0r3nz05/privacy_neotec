# Flujo de mensajes OAuth

- [Flujo de mensajes OAuth](#flujo-de-mensajes-oauth)
  - [Foco de informe](#foco-de-informe)
    - [Flujo del protocolo: Entorno centralizado/federado](#flujo-del-protocolo-entorno-centralizadofederado)
    - [Flujo del protocolo: Self-Issued OpenID Provider](#flujo-del-protocolo-self-issued-openid-provider)
  - [Arquitectura de Self-Issued OpenID Provider](#arquitectura-de-self-issued-openid-provider)
  - [Mensajes](#mensajes)
    - [Create Authentication Request](#create-authentication-request)
    - [verifyAuthenticationRequest](#verifyauthenticationrequest)
    - [resolveDID](#resolvedid)
    - [Create Authentication Response](#create-authentication-response)
    - [Verify Authentication Response](#verify-authentication-response)
    - [Create AccessToken](#create-accesstoken)
    - [Sessions verifyAccessToken](#sessions-verifyaccesstoken)
  - [Software Bill of Material (SBOM)](#software-bill-of-material-sbom)
  - [Referencias](#referencias)

## Foco de informe

La siguiente figura ilustra donde se pone el foco del estudio.

   ![alt text](./img/focus.png "Focus")

### Flujo del protocolo: Entorno centralizado/federado

```bash
+--------+                                   +--------+
|        |                                   |        |
|        |---------(1) AuthN Request-------->|        |
|        |                                   |        |
|        |  +--------+                       |        |
|        |  |        |                       |        |
|        |  |  End-  |<--(2) AuthN & AuthZ-->|        |
|        |  |  User  |                       |        |
|   RP   |  |        |                       |   OP   |
|        |  +--------+                       |        |
|        |                                   |        |
|        |<--------(3) AuthN Response--------|        |
|        |                                   |        |
|        |---------(4) UserInfo Request----->|        |
|        |                                   |        |
|        |<--------(5) UserInfo Response-----|        |
|        |                                   |        |
+--------+                                   +--------+
```

1. The RP (Client) sends a request to the OpenID Provider (OP).
2. The OP authenticates the End-User and obtains authorization.
3. The OP responds with an ID Token and usually an Access Token.
4. The RP can send a request with the Access Token to the UserInfo Endpoint.
5. The UserInfo Endpoint returns Claims about the End-User.

### Flujo del protocolo: Self-Issued OpenID Provider

```bash
+------+                                           +----------------+
|      |                                           |                |
|      |--(1) Self-Issued OpenID Provider Request->|                |
|      |     (Authentication Request)              |                |
|      |                                           |                |
|      |       +----------+                        |                |
|      |       |          |                        |                |
|      |       | End-User |                        |                |
|  RP  |       |          |<--(2) AuthN & AuthZ--->| Self-Issued OP |
|      |       |          |                        |                |
|      |       +----------+                        |                |
|      |                                           |                |
|      |<-(3) Self-Issued OpenID Provider Response-|                |
|      |      (Self-Issued ID Token)               |                |
|      |                                           |                |
+------+                                           +----------------+
```

## Arquitectura de Self-Issued OpenID Provider

   ![alt text](./img/did-oidc_siop_v2.png "Focus")

- [Código Fuente del diagrama](./img/DID-OIDC_SIOP_sequence_diagram.txt)
- [Herramienta de edición: PlantUML](https://plantuml.com/)

## Mensajes

### Create Authentication Request

La Relying Party (RP) llama a *createAuthenticationRequest* para generar una URL con un token de solicitud de autenticación DID incrustado en la URL.

```bash
  openid://?response_type=id_token
      &client_id=https%3A%2F%2Frp.example.com%2Fcb
      &scope=openid%20did_authn
      &request=<JWT>
```

### verifyAuthenticationRequest

Validar una solicitud de autenticación OAuth2 siguiendo esta RFC - DID-OAuth2 en EBSI V2

```bash
    openid://?
        scope=openid%20profile
        &response_type=id_token
        &client_id=https%3A%2F%2Fclient.example.org%2Fpost_cb
        &redirect_uri=https%3A%2F%2Fclient.example.org%2Fpost_cb
        &response_mode=post
        &claims=...
        &registration=%7B%22subject_syntax_types_supported%22%3A
        %5B%22urn%3Aietf%3Aparams%3Aoauth%3Ajwk-thumbprint%22%5D%2C%0A%20%20%20%20
        %22id_token_signing_alg_values_supported%22%3A%5B%22ES256%22%5D%7D
        &nonce=n-0S6_WzA2Mj
```

### resolveDID

Dado un DID, intenta resolver si es un DID de EBSI (did:ebsi:0x..) al usuario de la API de DID de EBSI, o utiliza el Resolutor de DID Universal para resolverlo.

**Input:**

```bash
    const didDocument = VerifiablePresentationLib.resolveDid("did:ebsi:0xE3f80bcbb360F04865AfA795B7507d384154216C");
```

**Output:**

```bash
    console.log(didDocument);
    {
          "@context": "https://w3id.org/did/v1",
          "id": "did:ebsi:0xE3f80bcbb360F04865AfA795B7507d384154216C",
          "controller": "did:ebsi:0xE3f80bcbb360F04865AfA795B7507d384154216C",
          "authentication": [
            {
              "type": "EcdsaSecp256k1VerificationKey2019",
              "publicKey": "did:ebsi:0xE3f80bcbb360F04865AfA795B7507d384154216C#key-1"
            }
          ],
          "verificationMethod": [
            {
              "id": "did:ebsi:0xE3f80bcbb360F04865AfA795B7507d384154216C#key-1",
              "type": "EcdsaSecp256k1VerificationKey2019",
              "controller": "did:ebsi:0xE3f80bcbb360F04865AfA795B7507d384154216C",
              "publicKeyJwk": {
                "kty": "EC",
                "crv": "secp256k1",
                "x": "62451c7a3e0c6e2276960834b79ae491ba0a366cd6a1dd814571212ffaeaaf5a",
                "y": "1ede3d754090437db67eca78c1659498c9cf275d2becc19cdc8f1ef76b9d8159"
              }
            }
          ]
        }
```

### Create Authentication Response

Valida una respuesta de autenticación OAuth2 utilizando el protocolo AKE incluyendo el token de acceso en la respuesta y devuelve el token de *acceso descifrado*.

```bash
    HTTP/1.1 200 OK
      Content-Type: application/json
      Cache-Control: no-store
      Pragma: no-cache

      {
       "access_token": "SlAV32hkKG",
       "token_type": "Bearer",
       "expires_in": 3600,
       "id_token": "..."
      }
```

### Verify Authentication Response

Validar una respuesta de autenticación OAuth2 usando el protocolo AKE incluyendo el Access Token en la respuesta y devuelve el Access Token descifrado.

Valide el token de acceso:

Si la firma es válida, se valida la cabecera JWT

1. alg claim must be ES256K
2. typ MUST be JWT
3. kid MUST be a URI to the public signing key of the Authorisation API

y se valida el cuerpo:

4. iss claim MUST be "Authorisation API"
5. sub claim MUST be application name that is accessing the endpoint. Sub claim is optional for entities.
6. aud claim MUST be the resource app name.
7. atHash claim MUST be hash of the access token
8. iat/exp claims MUST be the issuance and expiry dates
9.  nonce claim MUST be a random string


### Create AccessToken

Crea un nuevo objeto de sesión, devolviendo el token de acceso utilizando el protocolo AKE.


### Sessions verifyAccessToken

Un componente EBSI (servidor de recursos) recibe un token de acceso como portador de un cliente para acceder a un recurso protegido, y llama a la biblioteca para verificar el token de sesión siguiendo este proceso de validación, comprobando que es emitido por el componente de autorización.

## Software Bill of Material (SBOM)

| Library Name          |        Issuer                    |   Status              |
|-----------------------|----------------------------------|-----------------------|
|[SIOP DID Profile](https://github.com/decentralized-identity/did-siop) | Decentralized Identity Foundation | Deprecated            |
|[Self Issued OpenID Provider v2 (SIOP)](https://github.com/Sphereon-Opensource/did-auth-siop)| Sphereon-Opensource| Last Update: 05/07/22 |
|[EBSI SIOP Auth Library](https://www.npmjs.com/package/@cef-ebsi/siop-auth)| EBSI                              | Last Update: 01/08/22 |
|[Verifiable Credential](https://www.npmjs.com/package/@cef-ebsi/verifiable-credential)| EBSI                              | Last Update: 02/08/22 |
|[Verifiable Presentation](https://www.npmjs.com/package/@cef-ebsi/verifiable-presentation)| EBSI                              | Last Update: 02/08/22 |
|[DID Resolver](https://www.npmjs.com/package/@cef-ebsi/ebsi-did-resolver)           | EBSI                              | Last Update: 01/08/22 |

## Referencias

1. [Where to begin with OIDC and SIOP](https://medium.com/decentralized-identity/where-to-begin-with-oidc-and-siop-7dd186c89796), Medium Article.
2. [Self-Issued OpenID Provider v2](https://openid.net/specs/openid-connect-self-issued-v2-1_0.html), OpenID Standard.
3. [OpenID for Verifiable Presentations](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html), OpenID Standard.
4. [EBSI Documentation Home](https://ec.europa.eu/digital-building-blocks/wikis/display/EBSIDOC), EBSI.