# API-Tutorials

<details>
<summary><strong>EM (Enterprise Management / Event Management) service</strong></summary>

EM service is likely an Enterprise Management / Event Management service that acts as an orchestrator, and it internally triggers BI services (Business Intelligence services).

Typical flow:
```
Frontend/UI
    ↓
EM Service
    ↓
BI Services
    ↓
Database / Analytics Engine
```
The frontend does NOT directly call BI. EM acts as the mediator/orchestrator layer.

EM Service
- Main backend service
- Receives requests from UI or other systems
- Validates auth/session
- Applies business rules
- Calls downstream services

BI Services
- Business Intelligence / Reporting services
- Generate reports, analytics, dashboards
- Fetch aggregated data
- Process KPIs/metrics

Example scenario:
1. User clicks “Generate Sales Report”
2. Frontend calls EM service
3. EM service validates request
4. EM service internally triggers BI service
5. BI service generates analytics/report
6. Response returns to UI

**When testing in Fiddler:**

You usually see:
- Request from UI → EM service
- EM service response

But:
- Internal EM → BI calls may NOT appear in Fiddler unless:
  - traffic passes through proxy
  - service logs are enabled
  - distributed tracing is configured
To verify internal BI triggering, teams commonly use:
- Application logs
- Correlation IDs
- Kibana/Grafana
- Splunk
- Jaeger/Zipkin tracing
- API gateway logs

**Example technical stack:**
- EM service → Java Spring Boot / .NET / Node.js
- BI service → Reporting engine, analytics microservice, ETL APIs
- Communication → REST, gRPC, Kafka, RabbitMQ


**In enterprise systems, this architecture is very common in:**
- Banking
- Telecom
- Insurance
- ERP platforms
- Analytics dashboards
- Reporting systems

</details>

<details>
<summary><strong>Why companies use an EM service as middleware ?</strong></summary>

**1. Security Isolation**

BI platforms often expose:
- sensitive datasets
- analytics APIs
- enterprise credentials
- tenant-level access

If frontend directly calls BI:
- API keys may leak
- tokens may be exposed
- row-level security becomes harder
- users may bypass restrictions

EM service:
- stores secrets securely
- validates JWT/session
- applies RBAC/ABAC
- generates scoped access tokens

**2. Centralized Business Logic**

BI services only provide analytics/data.

But enterprise apps usually need:
- custom filtering
- role-based transformations
- aggregation rules
- audit logging
- feature toggles
- tenant mapping

EM service handles:
```
user → permissions → dataset mapping → BI query
```
instead of exposing raw BI APIs.

3. Multi-source Orchestration

Often data comes from:
- BI service
- SQL DB
- CRM
- HRMS
- vector DB
- AI service

EM service can combine all:
```
Frontend
   ↓
EM Service
   ├── BI Service
   ├── PostgreSQL
   ├── AI Engine
   └── Redis Cache
```
Frontend gets one unified response.

4. Performance & Caching

BI queries can be expensive and slow.

EM service can:
- cache reports
- precompute analytics
- throttle requests
- batch queries
- reduce BI API cost

Without middleware:
- every user hits BI directly
- higher latency
- higher licensing/API cost

5. Vendor Independence

If frontend directly depends on BI APIs:
- changing BI vendor becomes difficult

Example:
- today: Power BI
- tomorrow: Tableau
- later: Looker

With EM layer:
```
Frontend remains unchanged
```
Only middleware integration changes.

6. Better Audit & Monitoring

EM service can log:
- who viewed what
- report usage
- failed requests
- suspicious activity
- analytics tracking

This is critical in enterprise systems.

**When direct BI calls ARE acceptable**

Direct frontend → BI works for:
- internal dashboards
- small teams
- prototypes
- trusted users
- embedded analytics
- low-security apps

Example:
```
embedding Power BI dashboard directly in Angular
```

</details>

<details>
<summary><strong>REST + WebSocket + Webhook</strong></summary>

| Diagram Section      | Technology         | Why               |
| -------------------- | ------------------ | ----------------- |
| Frontend → Backend   | **REST / GraphQL** | User actions      |
| Live search / status | **WebSocket**      | Real-time         |
| Payment sync         | **Webhook**        | External async    |
| Backend → Backend    | **REST / gRPC**    | Internal services |
| Affiliate tracking   | **Webhook**        | Event-driven      |
| Public APIs          | **REST / GraphQL** | Open consumption  |


</details>

<details>
<summary><strong>APIs Explained with Real Use Cases</strong></summary>

<img src="https://github.com/piyalidas10/API-AWS-Tutorials/blob/9088d78ab6145a1c99641f0002b8cae10ce0ffe4/img/API_Realtime.png" width="700" />

I classify APIs based on who consumes them: Open APIs, Internal APIs, and Partner APIs.  
  -  Open APIs : Open APIs include REST, SOAP, and GraphQL. REST is used for most public use cases, GraphQL for data-heavy UIs, and SOAP for legacy or regulated systems like banking.
  -  Internal APIs : Internal APIs are split into frontend-to-backend, backend-to-backend, and service-to-database. Angular only interacts with frontend APIs using REST, GraphQL, or WebSockets. Backend-to-backend communication is hidden and often uses REST or gRPC.
  -  Partner APIs : Partner APIs handle B2B, affiliate, and data-sharing use cases. These rely heavily on REST and Webhooks for asynchronous event-driven communication.
  -  Real-time & Security : For real-time updates, WebSockets are used. For external async events like payments, Webhooks are used. Each API type is secured differently — JWT for frontend, mTLS for services, and signatures for webhooks.
  -  REST handles commands, WebSockets handle live state, and Webhooks handle external events — all classified by consumer trust boundaries.

**🎯 Whiteboard-friendly version**
```
                              ┌─────────────┐
                              │     API     │
                              └─────┬───────┘
                                    │
        ┌───────────────────────────┼───────────────────────────┐
        │                           │                           │
   ┌────▼────┐                 ┌────▼────┐                 ┌────▼────┐
   │ Open API│                 │Internal │                 │Partner  │
   │ (Public)│                 │  API    │                 │  API    │
   └────┬────┘                 └────┬────┘                 └────┬────┘
        │                           │                           │
 ┌──────┼────────┐        ┌─────────┼──────────┐       ┌────────┼────────┐
 │      │        │        │         │          │       │        │        │
REST  SOAP   GraphQL  Frontend→BE  Backend→BE  Service→DB  B2B  Affiliate Data
 │      │        │        │         │          │       │        │        │
Login  Bank   Feed   Login/Profile  Payment    CRUD ops  Booking Tracking Sharing
Fetch  Govt   Stats  Live search    Stock upd  Reports   Gateway Clicks  Health
```

**🎯 Real System Design Mapping**
```
Angular App
 ├── REST → login, create order
 ├── WebSocket → live status, notifications
 └── GraphQL → dashboard data

Backend
 ├── REST → internal services
 ├── Webhook → payment, partner events
 └── Event Bus → Kafka / Redis

Partners
 └── Webhook → async callbacks
```
</details>

<details>
<summary><strong>Design a Real-Time order tracking system (e-commerce / food delivery / trading app)</strong></summary>

I use REST for transactional commands, WebSockets for pushing state changes to the UI, and Webhooks to ingest asynchronous external events. Internally, I decouple everything using an event bus so each component can scale independently.

**Requirements**
  -  User places an order
  -  Order status updates in real time
  -  External systems (payment, logistics) notify asynchronously
  -  UI updates instantly without refresh
  -  System must scale
```
┌──────────┐     REST      ┌────────────┐
│ Angular  │─────────────▶│ API Gateway │
│  App     │               └─────┬──────┘
│          │  WebSocket          │ REST
│          │◀──────────────────▶│
└────┬─────┘                     ▼
     │                      ┌────────────┐
     │                      │ Order Svc  │
     │                      └─────┬──────┘
     │                            │
     │        Webhook             ▼
     │◀────────────────────┌────────────┐
     │                     │ Payment    │
     │                     │ Gateway    │
     │                     └────────────┘
```
1️⃣ Order Creation → REST
--------------------------------------------------------------
**Why REST?**
  -  One-time request
  -  Reliable
  -  Easy retries

**Flow** : Angular → POST /orders → Order Service

Angular
```
placeOrder(order: Order) {
  return this.http.post('/api/orders', order);
}
```

Backend
```
POST /orders
→ validate
→ save DB
→ return orderId
```
> 📌 Interview line : “REST is ideal for transactional, request-response actions like order creation.”

2️⃣ Live Order Status → WebSocket
--------------------------------------------------------------
**Why WebSocket?**
  -  Server needs to push updates
  -  Polling is inefficient
  -  Real-time UX

**Connection** : Angular ↔ WebSocket Server

Angular
```
const socket$ = webSocket('wss://api/orders');

socket$.subscribe(msg => {
  if (msg.orderId === currentOrderId) {
    this.orderStatus.set(msg.status);
  }
});
```

Backend
```
onOrderStatusChange(orderId, status) {
  wsServer.broadcast({
    orderId,
    status
  });
}
```
> 📌 Interview line : “WebSockets maintain a persistent channel for server-driven updates.”

3️⃣ Payment Confirmation → Webhook
--------------------------------------------------------------
**Why Webhook?**
  -  External systems control the event
  -  Async & event-driven
  -  No polling

Flow : Payment Gateway → POST /webhook/payment

Backend Webhook
```
POST /webhook/payment
→ verify signature
→ update order status
→ publish event
```

Trigger WebSocket
```
eventBus.emit('ORDER_PAID', orderId);
```
> 📌 Interview line : “Webhooks decouple external systems and enable reliable async communication.”

4️⃣ Event Propagation (Internal)
--------------------------------------------------------------
```
Webhook
  ↓
Order Service
  ↓
Event Bus (Kafka / RabbitMQ)
  ↓
WebSocket Gateway
  ↓
Angular UI
```
**📌 Why Event Bus?**
  -  Loose coupling
  -  Scalability
  -  Retry & durability

🔐 Security Design (Very Important)
--------------------------------------------------------------
**REST**
  -  OAuth2 / JWT
  -  CSRF protection
  -  Rate limiting

**WebSocket**
  -  Token in handshake
```
new WebSocket('wss://api/orders?token=JWT');
```
  -  Auth check on connect

**Webhook**
  -  Signature validation
```
verifyHmac(req.body, signature);
```
  -  IP allowlist
> 📌 Interview line : “Each channel uses a security mechanism appropriate to its communication model.”

⚡ Scaling Considerations
--------------------------------------------------------------
**WebSocket Scaling**
Problem: Users connected to different servers
Solution: Redis Pub/Sub or Kafka
```
Order Service → Kafka → WS Server A/B/C
```
REST Scaling : Stateless services, Load balancer, Auto-scaling
Webhook Reliability : Idempotency keys, Retry handling, Dead-letter queue

| Requirement       | Technology | Reason            |
| ----------------- | ---------- | ----------------- |
| Create order      | REST       | Reliable & simple |
| Real-time updates | WebSocket  | Push-based        |
| External events   | Webhook    | Async & decoupled |
| Internal sync     | Event Bus  | Scalable          |
> “REST handles commands, WebSockets handle state changes, Webhooks handle external events.”

**🧪 Failure Scenarios (Interview Favorite)**
  -  ❌ WebSocket disconnect
  -  ✔ Fallback to REST polling
  -  ❌ Duplicate webhook
  -  ✔ Idempotency check
  -  ❌ Event bus down
  -  ✔ Persist events → retry later


</details>

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

**🎯 System-Design Decision Rule**
  -  Angular UI → REST / GraphQL
  -  Backend-to-Backend → gRPC
  -  Live UI updates → WebSockets
  -  Async notifications → Webhooks
  -  Video / Audio → WebRTC

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
<summary><strong>Authentication : When to Use API Keys, Basic, Bearer, OAuth2, JWT & SSO</strong></summary>

API Keys
--------------------------------------------------------------
API keys must never be embedded directly in frontend code (Angular/React/Vue).   
If an API key is in: environment.ts, JS bundle, Network request headers then 👉 It is already compromised.
```
// ❌ DO NOT DO THIS
this.http.get(
  'https://api.thirdparty.com/data',
  { headers: { 'x-api-key': 'abc123' } }
);
```
**✅ CORRECT PATTERN**  
```
Angular App
 → Your Backend (OAuth/JWT)
 → Third-Party API (API Key)
```
Flow
  -  Angular authenticates user (OAuth/JWT)
  -  Angular calls your backend
  -  Backend attaches API key
  -  Backend returns response

Angular code (safe)
```
this.http.get('/api/weather');
```
Backend code (Node example)
```
fetch('https://weather.api.com', {
  headers: { 'x-api-key': process.env.WEATHER_API_KEY }
});
```
> API keys should be shielded behind a BFF.

**Public API Keys (Allowed but Limited)**  
Some APIs intentionally allow frontend usage.
Examples
  -  Google Maps JS API
  -  Firebase config
  -  Analytics SDKs
Why this is allowed
  -  Keys are domain-restricted
  -  Read-only
  -  Rate-limited
  -  Monitored

Example (Angular)
```
<script
  src="https://maps.googleapis.com/maps/api/js?key=PUBLIC_KEY">
</script>
```
Security controls
  -  Domain allow-list
  -  Quota limits
  -  No sensitive operations

| Scenario            | Use API Key in Frontend?  |
| ------------------- | ------------------------- |
| Angular user auth   | ❌ No                     |
| Public map SDK      | ✅ Yes (restricted)       |
| Weather API         | ❌ No                     |
| Backend integration | ✅ Yes                    |
| Webhooks            | ✅ Yes (backend)          |

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

