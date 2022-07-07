# Authorization Examples

The following are non-normative examples of Authorization Requests with different response_type values and their responses (with line wraps within values for display purposes only):

## Example using response_type=code

  ```bash
  GET /authorize?
    response_type=code
    &client_id=s6BhdRkqt3
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
    &scope=openid%20profile%20email
    &nonce=n-0S6_WzA2Mj
    &state=af0ifjsldkj HTTP/1.1
  Host: server.example.com

  HTTP/1.1 302 Found
  Location: https://client.example.org/cb?
    code=Qcb0Orv1zh30vL1MPRsbm-diHiMwcLyZvn1arpZv-Jxf_11jnpEX3Tgfvk
    &state=af0ifjsldkj
  ```

## Example using response_type=id_token

  ```bash
  GET /authorize?
    response_type=id_token
    &client_id=s6BhdRkqt3
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
    &scope=openid%20profile%20email
    &nonce=n-0S6_WzA2Mj
    &state=af0ifjsldkj HTTP/1.1
  Host: server.example.com

  HTTP/1.1 302 Found
  Location: https://client.example.org/cb#
    id_token=eyJraWQiOiIxZTlnZGs3IiwiYWxnIjoiUlMyNTYifQ.ewogImlz
    cyI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4
    Mjg5NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAi
    bi0wUzZfV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEz
    MTEyODA5NzAsCiAibmFtZSI6ICJKYW5lIERvZSIsCiAiZ2l2ZW5fbmFtZSI6
    ICJKYW5lIiwKICJmYW1pbHlfbmFtZSI6ICJEb2UiLAogImdlbmRlciI6ICJm
    ZW1hbGUiLAogImJpcnRoZGF0ZSI6ICIwMDAwLTEwLTMxIiwKICJlbWFpbCI6
    ICJqYW5lZG9lQGV4YW1wbGUuY29tIiwKICJwaWN0dXJlIjogImh0dHA6Ly9l
    eGFtcGxlLmNvbS9qYW5lZG9lL21lLmpwZyIKfQ.rHQjEmBqn9Jre0OLykYNn
    spA10Qql2rvx4FsD00jwlB0Sym4NzpgvPKsDjn_wMkHxcp6CilPcoKrWHcip
    R2iAjzLvDNAReF97zoJqq880ZD1bwY82JDauCXELVR9O6_B0w3K-E7yM2mac
    AAgNCUwtik6SjoSUZRcf-O5lygIyLENx882p6MtmwaL1hd6qn5RZOQ0TLrOY
    u0532g9Exxcm-ChymrB4xLykpDj3lUivJt63eEGGN6DH5K6o33TcxkIjNrCD
    4XB1CKKumZvCedgHHF3IAK4dVEDSUoGlH9z4pP_eWYNXvqQOjGs-rDaQzUHl
    6cQQWNiDpWOl_lxXjQEvQ
    &state=af0ifjsldkj
  ```

1. The value of the id_token parameter is the ID Token, which is a signed JWT, containing three base64url encoded segments separated by period ('.') characters. The first segment represents the JOSE Header. Base64url decoding it will result i1. n the following set of Header Parameters:1. 

    ```bash
    {"kid":"1e9gdk7","alg":"RS256"}
    ```

    The alg value represents the algorithm that was used to sign the JWT, in this case RS256, representing RSASSA-PKCS-v1_5   using   SHA-256. The kid value is a key identifier used in identifying the key to be used to verify the signature. If the  kid value is  unknown to the RP, it needs to retrieve the contents of the OP's JWK Set again to obtain the OP's current set of keys.

2. The second segment represents the Claims in the ID Token. Verifying and decoding the ID Token will yield the following Claims:

    ```bash
    {
     "iss": "http://server.example.com",
     "sub": "248289761001",
     "aud": "s6BhdRkqt3",
     "nonce": "n-0S6_WzA2Mj",
     "exp": 1311281970,
     "iat": 1311280970,
     "name": "Jane Doe",
     "given_name": "Jane",
     "family_name": "Doe",
     "gender": "female",
     "birthdate": "0000-10-31",
     "email": "janedoe@example.com",
     "picture": "http://example.com/janedoe/me.jpg"
    }
    ```

3. The third segment represents the ID Token signature, which is verified as described in [JWS].

### Example using response_type=id_token token

  ```bash
  GET /authorize?
    response_type=id_token%20token
    &client_id=s6BhdRkqt3
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
    &scope=openid%20profile%20email
    &nonce=n-0S6_WzA2Mj
    &state=af0ifjsldkj HTTP/1.1
  Host: server.example.com

  HTTP/1.1 302 Found
  Location: https://client.example.org/cb#
    access_token=jHkWEdUXMU1BwAsC4vtUsZwnNvTIxEl0z9K3vx5KF0Y
    &token_type=Bearer
    &id_token=eyJraWQiOiIxZTlnZGs3IiwiYWxnIjoiUlMyNTYifQ.ewogIml
    zcyI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ
    4Mjg5NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiA
    ibi0wUzZfV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDE
    zMTEyODA5NzAsCiAiYXRfaGFzaCI6ICI3N1FtVVB0alBmeld0RjJBbnBLOVJ
    RIgp9.F9gRev0Dt2tKcrBkHy72cmRqnLdzw9FLCCSebV7mWs7o_sv2O5s6zM
    ky2kmhHTVx9HmdvNnx9GaZ8XMYRFeYk8L5NZ7aYlA5W56nsG1iWOou_-gji0
    ibWIuuf4Owaho3YSoi7EvsTuLFz6tq-dLyz0dKABMDsiCmJ5wqkPUDTE3QTX
    jzbUmOzUDli-gCh5QPuZAq0cNW3pf_2n4zpvTYtbmj12cVcxGIMZby7TMWES
    RjQ9_o3jvhVNcCGcE0KAQXejhA1ocJhNEvQNqMFGlBb6_0RxxKjDZ-Oa329e
    GDidOvvp0h5hoES4a8IuGKS7NOcpp-aFwp0qVMDLI-Xnm-Pg
    &state=af0ifjsldkj
  ```

Verifying and decoding the ID Token will yield the following Claims:

  ```bash
  {
   "iss": "http://server.example.com",
   "sub": "248289761001",
   "aud": "s6BhdRkqt3",
   "nonce": "n-0S6_WzA2Mj",
   "exp": 1311281970,
   "iat": 1311280970,
   "at_hash": "77QmUPtjPfzWtF2AnpK9RQ"
  }
  ```

### Example using response_type=code id_token

  ```bash
  GET /authorize?
    response_type=code%20id_token
    &client_id=s6BhdRkqt3
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
    &scope=openid%20profile%20email
    &nonce=n-0S6_WzA2Mj
    &state=af0ifjsldkj HTTP/1.1
  Host: server.example.com

  HTTP/1.1 302 Found
  Location: https://client.example.org/cb#
    code=Qcb0Orv1zh30vL1MPRsbm-diHiMwcLyZvn1arpZv-Jxf_11jnpEX3Tgfvk
    &id_token=eyJraWQiOiIxZTlnZGs3IiwiYWxnIjoiUlMyNTYifQ.ewogIml
    zcyI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ
    4Mjg5NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiA
    ibi0wUzZfV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDE
    zMTEyODA5NzAsCiAiY19oYXNoIjogIkxEa3RLZG9RYWszUGswY25YeENsdEE
    iCn0.XW6uhdrkBgcGx6zVIrCiROpWURs-4goO1sKA4m9jhJIImiGg5muPUcN
    egx6sSv43c5DSn37sxCRrDZZm4ZPBKKgtYASMcE20SDgvYJdJS0cyuFw7Ijp
    _7WnIjcrl6B5cmoM6ylCvsLMwkoQAxVublMwH10oAxjzD6NEFsu9nipkszWh
    sPePf_rM4eMpkmCbTzume-fzZIi5VjdWGGEmzTg32h3jiex-r5WTHbj-u5HL
    7u_KP3rmbdYNzlzd1xWRYTUs4E8nOTgzAUwvwXkIQhOh5TPcSMBYy6X3E7-_
    gr9Ue6n4ND7hTFhtjYs3cjNKIA08qm5cpVYFMFMG6PkhzLQ
    &state=af0ifjsldkj
  ```

Verifying and decoding the ID Token will yield the following Claims:

  ```bash
  {
   "iss": "http://server.example.com",
   "sub": "248289761001",
   "aud": "s6BhdRkqt3",
   "nonce": "n-0S6_WzA2Mj",
   "exp": 1311281970,
   "iat": 1311280970,
   "c_hash": "LDktKdoQak3Pk0cnXxCltA"
  }
  ```

### Example using response_type=code token

  ```bash
  GET /authorize?
    response_type=code%20token
    &client_id=s6BhdRkqt3
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
    &scope=openid%20profile%20email
    &nonce=n-0S6_WzA2Mj
    &state=af0ifjsldkj HTTP/1.1
  Host: server.example.com

  HTTP/1.1 302 Found
  Location: https://client.example.org/cb#
    code=Qcb0Orv1zh30vL1MPRsbm-diHiMwcLyZvn1arpZv-Jxf_11jnpEX3Tgfvk
    &access_token=jHkWEdUXMU1BwAsC4vtUsZwnNvTIxEl0z9K3vx5KF0Y
    &token_type=Bearer
    &state=af0ifjsldkj
  ```

### Example using response_type=code id_token token

  ```bash
  GET /authorize?
    response_type=code%20id_token%20token
    &client_id=s6BhdRkqt3
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb
    &scope=openid%20profile%20email
    &nonce=n-0S6_WzA2Mj
    &state=af0ifjsldkj HTTP/1.1
  Host: server.example.com

  HTTP/1.1 302 Found
  Location: https://client.example.org/cb#
    code=Qcb0Orv1zh30vL1MPRsbm-diHiMwcLyZvn1arpZv-Jxf_11jnpEX3Tgfvk
    &access_token=jHkWEdUXMU1BwAsC4vtUsZwnNvTIxEl0z9K3vx5KF0Y
    &token_type=Bearer
    &id_token=eyJraWQiOiIxZTlnZGs3IiwiYWxnIjoiUlMyNTYifQ.ewogIml
    zcyI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ
    4Mjg5NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiA
    ibi0wUzZfV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDE
    zMTEyODA5NzAsCiAiY19oYXNoIjogIkxEa3RLZG9RYWszUGswY25YeENsdEE
    iCn0.XW6uhdrkBgcGx6zVIrCiROpWURs-4goO1sKA4m9jhJIImiGg5muPUcN
    egx6sSv43c5DSn37sxCRrDZZm4ZPBKKgtYASMcE20SDgvYJdJS0cyuFw7Ijp
    _7WnIjcrl6B5cmoM6ylCvsLMwkoQAxVublMwH10oAxjzD6NEFsu9nipkszWh
    sPePf_rM4eMpkmCbTzume-fzZIi5VjdWGGEmzTg32h3jiex-r5WTHbj-u5HL
    7u_KP3rmbdYNzlzd1xWRYTUs4E8nOTgzAUwvwXkIQhOh5TPcSMBYy6X3E7-_
    gr9Ue6n4ND7hTFhtjYs3cjNKIA08qm5cpVYFMFMG6PkhzLQ
    &state=af0ifjsldkj
  ```

  Verifying and decoding the ID Token will yield the following Claims:

  ```bash
  {
   "iss": "http://server.example.com",
   "sub": "248289761001",
   "aud": "s6BhdRkqt3",
   "nonce": "n-0S6_WzA2Mj",
   "exp": 1311281970,
   "iat": 1311280970,
   "at_hash": "77QmUPtjPfzWtF2AnpK9RQ",
   "c_hash": "LDktKdoQak3Pk0cnXxCltA"
  }
  ```
