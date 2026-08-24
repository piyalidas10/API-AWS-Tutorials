# 🔐 CORS (Cross-Origin Resource Sharing)

## What it is

Browser-level security that controls which origins can call your API.

## Why it matters

- Restricts which websites can make requests to your API.
- Critical for preventing unauthorized domains from accessing API endpoints.

## Key headers
- Access-Control-Allow-Origin
- Access-Control-Allow-Credentials
- Access-Control-Allow-Methods

## Essential CORS Headers
| **Header**                           | **Purpose**                                                      | **Example**                            |
| ------------------------------------ | ---------------------------------------------------------------- | -------------------------------------- |
| **Access-Control-Allow-Origin**      | Specifies which domains can access this resource                 | `https://yourapp.com` or `*`           |
| **Access-Control-Allow-Methods**     | Specifies which HTTP methods are allowed                         | `GET, POST, PUT, DELETE`               |
| **Access-Control-Allow-Headers**     | Specifies which request headers are allowed                      | `Content-Type, Authorization`          |
| **Access-Control-Allow-Credentials** | Specifies whether cookies/authentication credentials can be sent | `true` *(only with a specific origin)* |
| **Access-Control-Max-Age**           | Specifies how long the browser can cache the preflight response  | `3600` seconds                         |

## Real attack scenario
```
User logged into bank.com
Attacker site evil.com tries to call bank API using cookies
CORS blocks the request in the browser.
```

## Best practices

❌ Never use:
```
Access-Control-Allow-Origin: *
```

**with credentials**  
✅ Allow only trusted domains    
✅ Use preflight validation (OPTIONS)    

## Preflight

A preflight validation request uses the HTTP OPTIONS method. 
Browsers send it automatically before cross-origin requests with custom headers or non-standard methods to check if the server permits the actual action.

**How CORS Preflight Works**
- Trigger: Sent when a request uses methods like PUT or DELETE, or custom headers.Method: Uses the OPTIONS verb.
- Headers: Includes Access-Control-Request-Method and Origin.
- Response: The server replies with allowed methods and origins via headers like Access-Control-Allow-Origin.




