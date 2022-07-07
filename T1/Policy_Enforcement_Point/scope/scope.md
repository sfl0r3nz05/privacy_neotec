# SCOPE

El *scope* es un mecanismo de OAuth 2.0 para limitar el acceso de una aplicación a la cuenta de un usuario. Una aplicación puede solicitar uno o más *scopes*, esta información se presenta al usuario en la pantalla de consentimiento, y el token de acceso emitido a la aplicación se limitará a los *scopes* concedidos.

La especificación OAuth permite al servidor de autorización o al usuario modificar los *scopes* concedidos a la aplicación en función con lo solicitado, aunque no hay muchos ejemplos de servicios que hagan esto en la práctica.

OAuth no define ningún valor concreto para los *scopes*, ya que depende en gran medida de la arquitectura y las necesidades internas del servicio.

## OAuth

### Token Information Request

    ```bash
    POST /token_info HTTP/1.1
    Host: authorization-server.com
    Authorization: Basic Y4NmE4MzFhZGFkNzU2YWRhN
    
    token=c1MGYwNDJiYmYxNDFkZjVkOGI0MSAgLQ
    ```

### Token Information Response

    ```bash
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    
    {
      "active": true,
      "scope": "read write email",
      "client_id": "J8NFmU4tJVgDxKaJFmXTWvaHO",
      "username": "aaronpk",
      "exp": 1437275311
    }
    ```

## OpenID Connect

En el caso de OpenID Connect, los *scopes* pueden utilizarse para solicitar que conjuntos específicos de información estén disponibles como *Claim Values*. Las reclamaciones solicitadas por los *scopes* son tratadas por los servidores de autorización como reclamaciones *Voluntary Claims*. OpenID Connect define los siguientes valores de ámbito que se utilizan para solicitar *Claims*:

    ```bash
    profile 
        OPTIONAL. Este scope solicita acceso a los siguientes claims del perfil por defecto del usuario: name, family_name,  given_name, middle_name, nickname, preferred_username, profile, picture, website, gender, birthdate, zoneinfo, locale, and   updated_at.
    email
        OPTIONAL. Este scope solicita acceso a los claims: email y email_verified.
    address
        OPTIONAL. Este scope solicita acceso al claim: address.
    phone
        OPTIONAL. Este scope solicita acceso a los claims: phone_number y phone_number_verified Claims.
    ```
