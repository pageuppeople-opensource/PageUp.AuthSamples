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


### Sequence diagram (in context of work compliance usage)
![Authorization code grant flow](https://www.websequencediagrams.com/cgi-bin/cdraw?lz=cGFydGljaXBhbnQgTmV3aGlyZS1icm93c2VyCgAQDFBhZ2VVcC1PbmJvYXJkaW5nLVBvcnRhbCAAGQ1FcXVpZmF4ACYUQXV0aHNlcnZlcgoKCgBbDyAtPgBIGTogdmlldyBvAHAJIHRhc2sgZGV0YWlsCmFjdGl2YXRlAIEHGQA9LmNsaWNrczogY29tcGxldGUAZxAKAIFuGS0-AIIvEDogcmVkaXJlY3QgdGhlIHVzZXIgdXNlciB0byBlAIIiBiAoZXhhbXBsZToACgguY29tL2N1c3RvbWVyeC9uAIMMBmxvZ2luKQCCIhQAgmoHOiBnZXQgbmV3IGhpcmUgACsFIHBhZ2Ugb24AgwwJAIIjCQCDHggAgycHADsNSXMAgSQGaXMgbG9nZ2VkIGludG8Ag1EIPyB0YWtlIGhpbSB0bwCBUgVmb3JtcwA1FUVsc2UsAIF6CgCBcwgAg30SAHwLAIIyEUdldACCPgllZAAqFm5vdGUgb3ZlcgCDaxphdXRoIACEeAYgdXJsIGlzIHNwZWNpZmljAIFBCGNsaWVudCAKAIMJCWh0dHBzOi8vdGVzdHVzLgCCfwV1YXQucGFnZXVwcGVvcGxlAIMpBm9ubmVjdC9hdXRob3JpemU_AEYGX2lkPTw8AAMJX3Byb3ZpZGVkX2J5XwA_Bj4-XG4mc2NvcGU9PDwAAwUAHAk-PiYAhDcIX3VyaT0AgQYIAIQbDWFsbGJhY2stZW5kcG9pbnQmcmVzcG9uc2VfdHlwZT1jb2RlCmVuZCBub3RlAIZZGwCHEQoAhSAUAIFgKACGexEAh2cLAIdzEQBnF0lzAIYiCgCEdgk_IFxuIElmIHllcywgc2VuZACGSwVhY2Nlc3MgY29kZSB0byAAiSwIAEEpZiBub3QsIFxuIGFzawCHGAp0bwCGNwdhbmQANzYAh3YRVQCGQBAsIG5vdwCICxMAiA4LYWxvbmcgd2l0aACBZwYAhFQHYXRpb24AgW4GKACBdQVxdWVyeSBzdHJpbmcgcGFyYW1ldGVyKQCFcCRIVFRQLzEuMSAzMDIgRm91bmQKTG9jAF0FAIVzCnd3dy52ZW5kb3IAhFwWP3Rva2VuAIR4CACFIQUAhgcsADkFXG4mY29kZT01ZGI4ZDU4ZmI2ZDQyNzEwNTNmNzRlZjNkZjVmNzI1ZjI2OWZkMWM5NjI1ODg3ZWVmY2NmMzIxMDY4YTFmMTFiAIYxGQCFewkgAIomHQCLJQpvADGBTwCLXgsAh2ATcG9zdCByZXF1ZXMAgXcFAI8mC1MAilcLcmlnaHQgb2YAj1wJUE9TVCAAgz0OIACETQgKSG9zdDogAIgTIUNvbnRlbnQtVHlwZTogYXBwbGkAhH0GL3gtd3d3LWZvcm0tdXJsZW5jb2RlZAoAik4MaWQAijsFAIpmB3NlY3JldD08PAADBgAVBm9kZT08PGNvZGVfcmVjZWl2ZWRfZnJvbV9hYm92ZV8AgW4HAIsCBWdyYW50AIoyBgCGYQ1fY29kZVxuAIp4FgCFfyAAinQKAIltFQCPNAppdmUAiXUGAIJBBmJhY2sAkBILeyAiAIZjBSI6ICJhc2RmYSIsAAsHAItkBSI6ICJiZWFyZXIiIH0AgxYXVACNXgVhaW1zIGNhbiBiZSBkZQCCTgUgLyB2ZXJpZmllZCB0d28gd2F5cywKb0EAji0FdXBwb3J0cyBvbmxpbmUgYXMgd2VsbCBhcyBvZmYADgUANgYAiDEGLgpGb3IAJAgADQwsAJQRCCBzaG91bGQgY2FsbACUTAcncyAAlBEKADsHAEkTADkKY2FuIGNhY2hlAJJSBXB1YmxpYyBrZXkgZnJvbQBBFApCbwCBQgplZCBieQCKMQZpbmcgLndlbGwta25vd24gY29uZmlndXIAil8GAI5MCHMuCgCQAggAj3oJZGM0AI91EgBACy9vcGVuaWQtAEUNAI8ACmRlAI4QGwCTARRWYWxpZGF0AIFsBgCDQQUgaW4AhCUKAJF8BmxlZgCDXBYgd2lsbCBjb250YQAvBwCUIQgncyBlbXBsb3llZSBJZCBhc3NpZ24AghwGAJdqBi4gXG5UaGlzAIMwCGIAg2kIZWQgYWdhaW5zAJVXBgA6DACBEwdpbnZpdACNKgYAhnMIAI5oBWxyZWFkeSBieSBwb2xsaW5nIHdvcmsAlmwFaWFuY2UAgnAJLgCRMQoAlRUUUgCOKxQAk0QFb3IAlnAFAJUVBSAvIGhvbWUAlgcFCgoKCgo&s=magazine)
