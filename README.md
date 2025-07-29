# API-Tutorials

### API provider
An API provider is an organization that exposes APIs. An API provider, also known as an API service provider, is an entity (individual or organization) that creates, manages, and offers Application Programming Interfaces (APIs) for use by other developers, businesses, or applications.

**Examples of API providers include:**  
------------------------------------------------------------------------------------------------
**Cloud service providers**: Amazon Web Services (AWS), Google Cloud Platform, Microsoft Azure, which offer a wide range of APIs for various cloud services.  
**Communication platforms**: Twilio (for messaging and voice APIs), SendGrid (for email APIs).  
**Payment gateways**: Stripe (for online payment processing APIs).  
**Social media platforms**: Facebook, Twitter, Instagram, which provide APIs for accessing their platform data and functionalities.  

### API Consumers
Consumers are the entities that consume these APIs.  
There are three different types of API consumers. 1) The first type of consumer is the private or internal consumer. These consumers are part of the same organization that is building and exposing the APIs. 2) Second type is the public or external API consumer. These are outside of the provider organization. 3) The third type is the partner API consumer. These consumers are trusted by the API provider.  

### Types of API
Based on these three different API consumer types, there are three different types of APIs. 1) The **private or the internal APIs** are used only by the development teams within the provider's organization. 2) The **public or the external APIs** are meant to be consumed by the public domain application developers. These public domain application developers consume the APIs from their applications and websites. 3) **Partner APIs** are used only by the trusted partners of the provider's organization. 

One important point to note is that there is no difference in terms of the implementation of the API for these three types. The difference is in how these APIs are managed.

### SOAP vs REST vs On-Premise (on-prem)
SOAP (Simple Object Access Protocol) and REST (Representational State Transfer) are two distinct approaches for building web services that enable communication between different systems.

SOAP API:
------------------------------------------------------------------------------------------------
**Protocol-based**: SOAP is a formal, XML-based messaging protocol with strict standards and a defined structure.  
**Strictness**: It requires specific XML message formats (envelopes) and relies on WSDL (Web Services Description Language) for service description.  
**Security**: Offers built-in security features like WS-Security, in addition to supporting SSL/TLS.  
**Use Cases**: Often favored in enterprise environments requiring high security, transactional integrity, and complex business logic, such as banking or financial services.  

REST API:
------------------------------------------------------------------------------------------------
**Architectural Style**: REST is an architectural style that leverages standard HTTP methods (GET, POST, PUT, DELETE) for stateless communication.  
**Flexibility**: It is more flexible and lightweight, commonly using JSON or XML for data exchange.  
**Ease of Use**: Generally easier to implement and consume due to its reliance on standard web technologies.  
**Use Cases**: Widely adopted for web services, mobile applications, and public APIs where simplicity, scalability, and performance are key.  

On-Premise (Premise)
------------------------------------------------------------------------------------------------
The term "on-premise" (or "on-premise deployment") refers to software or infrastructure that is installed and run locally on a company's own servers and within its own data center, rather than being hosted by a third-party cloud provider.  
**Relation to APIs**: When discussing APIs, "on-premise" signifies that the API's underlying application and data reside within the company's private infrastructure. This contrasts with cloud-based APIs, where the service is hosted and managed by a cloud provider.  
**Implications**: On-premise deployments offer greater control over data security and compliance but require significant upfront investment in hardware, software licenses, and ongoing maintenance by the organization.  
