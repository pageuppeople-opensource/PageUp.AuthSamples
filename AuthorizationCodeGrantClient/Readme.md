## Authorization code grant configuration

1. Update the client id and secret
2. Ensure the client is configured with the correct return url in the auth server. It looks something similar to `http://localhost:5002/signin-oidc`
3. Grant type should be at least `authorization_code`
4. Scope include at least `user profile your user identifier`


### Interaction 1: Redirecting the Employee to the Auth server

#### Request

```
GET https://testus.loginuat.pageuppeople.com/connect/authorize?client_id=fdfab3cf-f175-4535-8a88-8c1948fdb0d6&scope=Compliance.Write%20Compliance.Read&state=8273682&redirect_uri=ttps://www.vendor.com/callback-endpoint&response_type=code HTTP/1.1
Host: testus.loginuat.pageuppeople.com
Accept: text/html
```

#### Response

```
HTTP/1.1 302 Found
Date: Mon, 19 Jun 2017 05:07:07 GMT
Content-Length: 0
Connection: keep-alive
Server: Kestrel
Cache-Control: no-store, no-cache, max-age=0
Pragma: no-cache
Location: https://www.vendor.com/callback-endpoint?tokenendpoint=http://testus.loginuat.pageuppeople.com/connect/token&code=5db8d58fb6d4271053f74ef3df5f725f269fd1c9625887eefccf321068a1f11b&scope=Compliance.Write%20Compliance.Read&state=8273682
```

Between the request above and its response, the end user will be navigated away to PageUp's auth server where the employee logs into the PageUp system. Upon successful login, the user browser gets redirected with the above response.

### Interaction 2: vendor server exchange the authorization code with PageUp Auth server

#### Request

```
POST /connect/token HTTP/1.1
Host: testus.loginuat.pageuppeople.com
Content-Type: application/x-www-form-urlencoded
Cache-Control: no-cache
client_id=<<id>>&client_secret=<<secret>>&code=<<code_received_from_above_request>>&grant_type=authorization_code&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2Fsignin-oidc
```

#### Resposne

```
{token:'<<generatedtoken>>',scheme: 'bearer'}
```
