https://docs.hashicorp.com/sentinel/language/

Sentinel policies are written using the Sentinel language. 

The http import enables the use of HTTP-accessible data from outside the runtime in Sentinel policy rules.

At the center of the import is the get() function, which issues a GET request and returns a response type:

import "http"

resp = http.get("https://example.hashicorp.com")
main = rule { resp.body contains "something" }

By default, any request response that is not a successful "200 OK" HTTP response causes a policy error. See [accept_status_codes](https://docs.hashicorp.com/sentinel/imports/http/#client-accept_status_codes-list) for more information and how to customize this behavior.


For more advanced requests which require setting request headers, use a request type:
import "http"
import "json"

param token

req = http.request("https://example.hashicorp.com").with_header("Authorization", "token "+token)
resp = json.unmarshal(http.get(req).body)
main = rule { resp["some_key"] is true }

The with_header function above returns the request object with the Authorization HTTP header set to value of the variable some_value. Many of the methods within the http import API are designed around this concept of method chaining, allowing for complex building of HTTP requests encapsulated in functions. The following is an idiomatic example further demonstrating this pattern:



Import: strings
The strings import exposes common string operations.

»strings.has_prefix(s, prefix)


strings.has_suffix(s, suffix)
»strings.join(a, sep)
»strings.trim_prefix(s, prefix)
»strings.trim_suffix(s, suffix)
»strings.to_lower(s)
»strings.to_upper(s)
»strings.split(s, sep)
refer from [here](https://docs.hashicorp.com/sentinel/imports/strings/)
