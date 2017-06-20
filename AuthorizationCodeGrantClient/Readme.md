## Authorization code grant flow

[RFC reference](https://tools.ietf.org/html/rfc6749#section-4.1)

### Screen cast of working sample

![Demo](../Assets/auth_code_grant_218.gif "sample app")

### Interaction 1: Redirecting the Employee to the Auth server

#### Request

```
GET https://testus.loginuat.pageuppeople.com/connect/authorize?client_id=<<client_id_provided_by_pageup>>&scope=<<scope_provided>>&redirect_uri=https://www.vendor.com/callback-endpoint&response_type=code HTTP/1.1
Accept: text/html
```

#### Response

```
HTTP/1.1 302 Found
Location: https://www.vendor.com/callback-endpoint?tokenendpoint=http://testus.loginuat.pageuppeople.com/connect/token&code=5db8d58fb6d4271053f74ef3df5f725f269fd1c9625887eefccf321068a1f11b&scope=<<scope_provided>>
```

Between the request above and its response, the end user will be navigated away to PageUp's Authentication server where the employee logs into the PageUp system. Upon successful login, the user browser gets redirected with the above response.

### Interaction 2: Vendor server exchanges the authorization code with the PageUp Authentication server

#### Request

```
POST /connect/token HTTP/1.1
Host: testus.loginuat.pageuppeople.com
Content-Type: application/x-www-form-urlencoded
client_id=<<id>>&client_secret=<<secret>>&code=<<code_received_from_above_request>>&grant_type=authorization_code&redirect_uri=https://www.vendor.com/callback-endpoint
```

#### Response

```
{ "access_token": "some_random_token", "expires_in": 3600, "token_type": "Bearer" }
```

#### Running the sample

1. Update the client id, secret and scopes in `Startup.cs`
2. Ensure the client is configured with the correct return url in the auth server (talk to PageUp rep). It looks something similar to `https://www.vendor.com/callback-endpoint`

#### PageUp notes
While configuring the client in our auth server
1. Ensure the client has `authorization_code` flow enabled
2. Ensure the selected scopes include `openid`, `profile`
