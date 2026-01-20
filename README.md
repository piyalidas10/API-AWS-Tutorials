# API-Tutorials

<details>
<summary><strong>API Security</strong></summary>
[![API Security Explained: Rate Limiting, CORS, SQL Injection, CSRF, XSS & More]](https://www.youtube.com/watch?v=FsB_nRGdeLs)

Rate Limiting
---------------------------------------------------------------------
  -  Controls how often a user or client can call your API.
  -  Helps prevent brute-force attacks (login, OTP) and API abuse by limiting requests per IP or user.
  -  🛡️ Security: Prevents brute-force attacks (login, OTP), credential stuffing, and DoS.
  -  ⚖️ Fair usage: Stops one client from starving others.
  -  🚀 Performance & stability: Avoids overload and cascading failures.
  -  💰 Cost control: Limits excessive usage of paid resources (DB, 3rd-party APIs).

<img src="https://github.com/piyalidas10/API-AWS-Tutorials/blob/75f1a37001be9102258c7bfa052ef22f931655f2/img/Rate_limiting.png" width="500">

Rate limiting protects APIs from abuse and ensures system stability by controlling request frequency. It’s best enforced at the API gateway using algorithms like token bucket or sliding window, often backed by Redis for distributed consistency. Clients exceeding limits receive HTTP 429 responses with retry metadata.

**Where Rate Limiting Is Implemented**  
🔹 API Gateway (Recommended) : NGINX, Kong, AWS API Gateway, Azure API Management, Cloudflare  
➡️ Centralized, language-agnostic, scalable  

🔹 Backend Application : Express / NestJS middleware, Spring Boot filters, .NET middleware  
➡️ Good for fine-grained, user-aware limits  

🔹 Distributed Cache : Redis (most common), Memcached  
➡️ Required for horizontal scaling  

**Rate Limiting Dimensions**  
You can limit by: , IP address, User ID, API key, JWT claims (role, plan), Endpoint, Tenant / Organization
  -  Free user: 100 req/min
  -  Premium user: 1000 req/min
  -  Admin: unlimited

**HTTP Response When Limit Exceeded**
```
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1700000000
```

CORS (Cross-Origin Resource Sharing)
---------------------------------------------------------------------
  -  Restricts which websites can make requests to your API.
  -  Critical for preventing unauthorized domains from accessing API endpoints.
<img src="https://github.com/piyalidas10/API-AWS-Tutorials/blob/75f1a37001be9102258c7bfa052ef22f931655f2/img/cors.png" width="500">

SQL Injection Prevention
---------------------------------------------------------------------
  -  Techniques to stop attackers from injecting malicious SQL code.
  -  Use parameterized queries or ORM tools to safely interact with databases.
<img src="https://github.com/piyalidas10/API-AWS-Tutorials/blob/75f1a37001be9102258c7bfa052ef22f931655f2/img/sql_nosql.png" width="500">

> Key headers : Access-Control-Allow-Origin, Access-Control-Allow-Credentials, Access-Control-Allow-Methods

> Real attack scenario : User logged into bank.com. Attacker site evil.com tries to call bank API using cookies. CORS blocks the request in the browser.

> ❌ Never use: Access-Control-Allow-Origin: *

> with credentials : ✅ Allow only trusted domains ✅ Use preflight validation (OPTIONS)

CSRF Protection
---------------------------------------------------------------------
Attacker injects malicious input that alters database queries.   
**Can lead to**: Data leakage, Data deletion, Admin access   
**Prevention** : Parameterized queries, ORM / Query builders, Input validation  
  -  Prevents unauthorized commands sent by malicious sites on behalf of an authenticated user.
  -  Often mitigated using tokens or SameSite cookies.

> Real scenario : Login API builds SQL with string concatenation. Attacker logs in without password

> Best practices : ✅ Never trust user input ✅ Use prepared statements ✅ Least-privilege DB users

<img src="https://github.com/piyalidas10/API-AWS-Tutorials/blob/75f1a37001be9102258c7bfa052ef22f931655f2/img/csrf.png" width="500">

XSS (Cross-Site Scripting) Defense
---------------------------------------------------------------------
XSS (Cross-Site Scripting) is injecting malicious JavaScript into trusted websites.  
**Allows**: Token theft | Session hijacking | UI manipulation  
**Types** : Stored XSS | Reflected XSS | DOM-based XSS  
  -  Ensures your API isn’t exploited to deliver harmful scripts to users.
<img src="https://github.com/piyalidas10/API-AWS-Tutorials/blob/75f1a37001be9102258c7bfa052ef22f931655f2/img/xss.png" width="500">

> Types : Network firewall (IP, ports) | Web Application Firewall (WAF) – API-aware

> Real scenario : Attacker sends XSS payload in query param | WAF blocks request before backend

Firewalls & Traffic Filtering
---------------------------------------------------------------------
Filters traffic before it reaches your API.
  -  Blocks: Known attack patterns, Malformed requests, Bot traffic
  -  Types : Network firewall (IP, ports) | Web Application Firewall (WAF) – API-aware
  -  Real scenario : Attacker sends XSS payload in query param. WAF blocks request before backend
<img src="https://github.com/piyalidas10/API-AWS-Tutorials/blob/200b60a3268211d54726f9ee7d85ac3068d905ba/img/firewall.png" width="500">

VPN / Private API Access
---------------------------------------------------------------------
Restricting APIs to private networks. Even if credentials leak, APIs remain unreachable.
**Common setups** : Corporate VPN, VPC-internal APIs, Zero-trust networks  
> Real scenario : Internal admin API accidentally exposed | Attackers scan & exploit | VPN would make it unreachable.
  -  Restricts access to internal APIs using network isolation, VPNs, or private endpoints
<img src="https://github.com/piyalidas10/API-AWS-Tutorials/blob/200b60a3268211d54726f9ee7d85ac3068d905ba/img/vpn.png" width="500">

Best practices : ✅ Public APIs only when required ✅ Internal APIs → private network only ✅ Combine with IAM

</details>

<details>
<summary><strong>API provider</strong></summary>
An API provider is an organization that exposes APIs. An API provider, also known as an API service provider, is an entity (individual or organization) that creates, manages, and offers Application Programming Interfaces (APIs) for use by other developers, businesses, or applications.

**Examples of API providers include:**  
------------------------------------------------------------------------------------------------
**Cloud service providers**: Amazon Web Services (AWS), Google Cloud Platform, Microsoft Azure, which offer a wide range of APIs for various cloud services.  
**Communication platforms**: Twilio (for messaging and voice APIs), SendGrid (for email APIs).  
**Payment gateways**: Stripe (for online payment processing APIs).  
**Social media platforms**: Facebook, Twitter, Instagram, which provide APIs for accessing their platform data and functionalities. 
</details>

<details>
<summary><strong>API Consumers</strong></summary>
Consumers are the entities that consume these APIs.  
There are three different types of API consumers. 1) The first type of consumer is the private or internal consumer. These consumers are part of the same organization that is building and exposing the APIs. 2) Second type is the public or external API consumer. These are outside of the provider organization. 3) The third type is the partner API consumer. These consumers are trusted by the API provider.  
</details>

<details>
<summary><strong>Types of API</strong></summary>
Based on these three different API consumer types, there are three different types of APIs. 1) The **private or the internal APIs** are used only by the development teams within the provider's organization. 2) The **public or the external APIs** are meant to be consumed by the public domain application developers. These public domain application developers consume the APIs from their applications and websites. 3) **Partner APIs** are used only by the trusted partners of the provider's organization. 

One important point to note is that there is no difference in terms of the implementation of the API for these three types. The difference is in how these APIs are managed.

## SOAP vs REST vs On-Premise (on-prem)
SOAP (Simple Object Access Protocol) and REST (Representational State Transfer) are two distinct approaches for building web services that enable communication between different systems.

SOAP API:
------------------------------------------------------------------------------------------------
**Protocol-based**: SOAP is a formal, XML-based messaging protocol with strict standards and a defined structure.  
**Strictness**: It requires specific XML message formats (envelopes) and relies on WSDL (Web Services Description Language) for service description.  
**Security**: Offers built-in security features like WS-Security, in addition to supporting SSL/TLS.  
**Use Cases**: Often favored in enterprise environments requiring high security, transactional integrity, and complex business logic, such as banking or financial services.  
**Key features:**
  -  Uses XML envelopes for all requests and responses.
  -  Works over HTTP but can also use other transport protocols (SMTP, TCP).
  -  Very strict about structure and error control.

REST API:
------------------------------------------------------------------------------------------------
**Architectural Style**: REST is an architectural style that leverages standard HTTP methods (GET, POST, PUT, DELETE) for stateless communication.  
**Flexibility**: It is more flexible and lightweight, commonly using JSON or XML for data exchange.  
**Ease of Use**: Generally easier to implement and consume due to its reliance on standard web technologies.  
**Use Cases**: Widely adopted for web services, mobile applications, and public APIs where simplicity, scalability, and performance are key.  

**Key features:**
  -  Stateless: Every request is independent (server doesn’t store session).
  -  Usually returns JSON (can also use XML).
  -  Works on URLs (“endpoints”) that map to real objects (like /users).

GraphQL
------------------------------------------------------------------------------------------------
A query language and API style where the client defines exactly what data it wants.  
**Key features:**
  -  All requests go to a single endpoint.
  -  Eliminates over-fetching or under-fetching of data.
  -  Supports nested queries and real-time subscriptions.

**Use cases:** ✔ Rich front-ends with complex data needs ✔ Mobile apps where bandwidth and performance matter

WebHooks
------------------------------------------------------------------------------------------------
A server-to-client push mechanism — the server calls your system when certain events happen.  
**Key features:**
  -  Triggered automatically by an event (like payment success).
  -  The server sends data to your callback URL (HTTP POST).
  -  Saves you from constantly polling for updates.

**Use cases:** ✔ Payment gateways (Stripe, Razorpay) ✔ GitHub notifications ✔ Form submission alerts

WebSockets
------------------------------------------------------------------------------------------------
A persistent two-way connection between client and server.  
**Key features:**
  -  Keeps a channel open so both parties can send messages anytime.
  -  Great for low-latency real-time communication.

**Use cases:** ✔ Chat apps ✔ Live multiplayer games ✔ Streaming dashboards or stock tickers

WebRTC (Web Real-Time Communication)
------------------------------------------------------------------------------------------------
A protocol for peer-to-peer real-time audio, video, and data exchange directly between browsers/devices.  
**Key features:**
  -  Bypasses central servers for media/data transfer.
  -  Handles connection setup, NAT traversal, and quality management.

**Use cases:** ✔ Video calls (e.g., Zoom, Meet) ✔ Screen sharing ✔ Real-time data exchange

On-Premise (Premise)
------------------------------------------------------------------------------------------------
The term "on-premise" (or "on-premise deployment") refers to software or infrastructure that is installed and run locally on a company's own servers and within its own data center, rather than being hosted by a third-party cloud provider.  
**Relation to APIs**: When discussing APIs, "on-premise" signifies that the API's underlying application and data reside within the company's private infrastructure. This contrasts with cloud-based APIs, where the service is hosted and managed by a cloud provider.  
**Implications**: On-premise deployments offer greater control over data security and compliance but require significant upfront investment in hardware, software licenses, and ongoing maintenance by the organization.  
</details>

<details>
<summary><strong>Authentication Explained: When to Use Basic, Bearer, OAuth2, JWT & SSO</strong></summary>
  
Basic Authentication
--------------------------------------------------------------
  -  Sends username:password encoded in Base64 with each request.
  -  Base64 is just encoding — it’s not secure on its own (easy to decode).
  -  ✅ When to use: internal tools, simple prototypes, or legacy systems.
  -  ❌ Not great for: public APIs or apps handling sensitive data.

Bearer Tokens
--------------------------------------------------------------
A token (a string) that the client receives after login and sends on each request in an Authorization: Bearer <token> header.  
Better than Basic Auth because it doesn’t repeatedly expose passwords.
```
      
┌──────────┐          (1) Login Request               ┌──────────┐
│  Client  │────────────────────────────────────────▶ | Auth    │
└──────────┘                                          │  Server  │
                                                      └──────────┘                          
┌──────────┐           (2) Access Token Issued             │
│  Client  │◀──────────────────────────────────────────────┘
└──────────┘

                   (3) API Request with Token
┌──────────┐   Authorization: Bearer <token>   ┌──────────┐
│  Client  │──────────────────────────────────▶│   API    │
└──────────┘                                   └──────────┘
```

**Pros:**
  -  ✔ Works well with stateless APIs
  -  ✔ Doesn’t expose user credentials after login  
**Cons:**
  -  ✖ Tokens must be securely stored (or they can be stolen).
  -  ✖ If stolen, tokens can be reused until expired
  -  ✖ Still need token expiry/refresh logic.
**When to Use Bearer Tokens**
  -  ✔ APIs that issue their own tokens
  -  ✔ Microservices authenticating API consumers
  -  ✔ Systems that need simple, stateless token checks

Access Tokens vs Refresh Tokens
--------------------------------------------------------------
Modern authentication systems rely on short-lived access tokens for API requests and long-lived refresh tokens to maintain user sessions.
When an access token expires, the refresh token seamlessly obtains a new one in the background — keeping the user logged in without interruption.

Access Token
  -  Short-lived (minutes)
  -  Used to access APIs
  -  Reduces the window for token theft

Refresh Token
  -  Long-lived (days or weeks)
  -  Used to obtain new access tokens
  -  Never sent to APIs (only to auth server)

Why this model matters
  -  If an access token leaks → attacker has limited time.
  -  If a refresh token leaks → you can revoke it.

When to Use Access/Refresh Tokens
  -  ✔ Mobile apps
  -  ✔ SPAs (React, Angular, Vue)
  -  ✔ OAuth2 implementations
  -  ✔ Systems requiring long-lived sessions

OAuth2
--------------------------------------------------------------
A framework allowing users to grant access to their data without sharing passwords.
```
   (1) User clicks "Login"
┌──────────┐
│  Client  │
└────┬─────┘
     │ Redirect to Login Page
     ▼
┌──────────────┐
│ Authorization │
│    Server     │
└────┬─────┬────┘
     │     │
     │(2) User Authenticates
     │
     ▼
┌──────────────┐
│ Authorization │
│     Code      │
└────┬──────────┘
     │ Exchange Code for Tokens
     ▼
┌──────────────┐
│   Auth Server │── Issues ─▶ Access Token + Refresh Token + ID Token
└──────────────┘
```
Examples:
  -  “Login with Google”
  -  Giving GitHub access to a CI/CD tool
  -  Allowing a mobile app to post on behalf of a user

Pros
  -  Secure delegated access
  -  Works across organizations
  -  Supports scopes and permissions
  -  Backbone of most modern identity systems

Cons
  -  More complex to implement
  -  Requires redirect flows
  -  Needs careful token & scope management

When to Use OAuth2
  -  ✔ Social logins (Google, GitHub, Facebook)
  -  ✔ Enterprise logins (Azure AD, Okta)
  -  ✔ Mobile & SPA authentication
  -  ✔ Third-party integrations

JWT (JSON Web Tokens)
--------------------------------------------------------------
OAuth2 lets users sign in using a trusted provider like Google or GitHub, so your app never handles their actual password.

After authentication, the provider returns an access token, typically a JWT. This token is signed and includes user details. Since JWTs are stateless, the server doesn’t need a session store — just validate the token and extract the data.
![OAuth2](https://github.com/piyalidas10/API-AWS-Tutorials/blob/97865352b4445f43f0233c4194987ec58acb720b/img/JWT.gif)
A signed, encoded JSON payload used as an access token.
![OAuth2+JWT](https://github.com/piyalidas10/API-AWS-Tutorials/blob/97865352b4445f43f0233c4194987ec58acb720b/img/oauth2%2BJWT.webp)
```
┌──────────┐                                    ┌──────────┐
│  Client  │──────── Authorization: JWT ───────▶│   API    │
└──────────┘                                    └──────────┘
                         ▲
                         │
                Validate signature (HS256/RS256)
                         │
                         ▼
Example JWT payload:
{
  "sub": "123",
  "role": "admin",
  "exp": 1710000000
}
```
Pros
  -  Stateless (no DB lookup needed)
  -  Fast validation
  -  Can store user metadata
  -  Works well in distributed systems

Cons
  -  Large token size
  -  Difficult to revoke early
  -  Must protect signing keys

When to Use JWTs
  -  ✔ Microservices
  -  ✔ Serverless architectures
  -  ✔ APIs needing fast, stateless auth
  -  ✔ OAuth2 access tokens

SSO (Single Sign-On)
--------------------------------------------------------------
  -  Lets a user log in once and access multiple apps without re-authenticating.
  -  Uses centralized identity providers (often built on OAuth2 or SAML). Behind the scenes, it relies on identity standards such as OAuth2 and SAML.
![SSO](https://github.com/piyalidas10/API-AWS-Tutorials/blob/17e99c1c4abcee9391545f74461824a2915a229b/img/SSo.webp)

Examples:
  -  Google Workspace
  -  Microsoft Office 365
  -  Okta / OneLogin
  -  Corporate employee portals

How It Works
  -  A central identity provider authenticates the user
  -  Other systems trust that identity
  -  Methods: SAML, OAuth2, OpenID Connect (OIDC)

Pros
  -  Great user experience
  -  Centralized identity & access management
  -  Strong security controls (MFA, RBAC)

Cons
  -  Requires identity provider setup
  -  Misconfigurations can leak access
  -  Integration varies by protocol (SAML vs OIDC)

When to Use SSO
  -  ✔ Enterprise applications
  -  ✔ Multi-application environments
  -  ✔ Apps requiring MFA, RBAC, or audit trails

</details>

