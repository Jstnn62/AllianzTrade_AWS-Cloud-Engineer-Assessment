#Question1. What weaknesses can you see in the current architecture?

Internal APIs are publicly exposed, Even APIs intended for internal usage only are deployed as public endpoints.
Internet → CloudFront → API Gateway → Internal APIs

It increase Increases the attack surface , Internal APIs could be discovered through scanning ,Security relies mainly on authentication rather than network isolation

Better to seperate Public APIs , Internal APIs , Partner APIs

The other one is , API Gateway can be accessed directly , Regional API Gateway endpoints are public and an attacker could bypass the protection layer, so the WAF protection 
can be bypassed and increased risk of direct API abuse.

There is no network-level separation, everything flows through the same entry point the risk is internal services indirectly exposed to internet routes.

--------------------------------------------------------------------------------------------

#Question 2.Proposed architecture for internal and external APIs

Public API flow should be :
Client --> CloudFront --> AWS WAF --> Public API Gateway --> Backend Services

Internal services should use private networking :
Internal Applications --> VPC Endpoint / PrivateLink --> Private API Gateway --> Backend Services

The major improvement :

External APIs --> Public API Gateway
Internal APIs --> Private API Gateway

--------------------------------------------------------------------------------------------

#Question3. How can CloudFront route traffic to multiple API Gateways using path-based routing?

CloudFront supports path pattern routing using different origins.

/customers/* --> Customer API Gateway
/payments/* --> Payments API Gateway
/partners/* --> Partner API Gateway
/internal/* --> Internal API Gateway

Each API domain can be managed independently and cn be enables microservice ownership. It helps to easier scaling and deployments and also have different security rules per API


--------------------------------------------------------------------------------------------

#Question4. How to prevent direct access to API Gateway endpoints (bypassing CloudFront/WAF)?

API Gateway has a default public endpoint https://api-id.execute-api.region.amazonaws.com and attackers could access this endpoint directly. 

Solution :
Restrict API Gateway so it only accepts traffic from CloudFront.

Allow Source = CloudFront distribution and Deny all other sources , this ensures API Gateway only processes requests forwarded by CloudFront.

Convert API Gateway to private endpoint. 
CloudFront -->VPC Endpoint --> Private API Gateway

 Internet
                     │
                     ▼
                CloudFront
                     │
                     ▼
                  AWS WAF
                     │
         ┌───────────┴───────────┐
         ▼                       ▼
   Public API Gateway      Private API Gateway
      (External APIs)        (Internal APIs)
         │                       │
         ▼                       ▼
    Lambda / ECS             Lambda / ECS

    






