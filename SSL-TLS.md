[Back](./AWS.md)

# SSL/TLS

- SSL = Secure Sockets Layer
- TLS = Transport Layer Security
- An SSL Certificate allows traffic between your clients and your load balancer to be encrypted in transit (in-flight encryption)
- TLS is the newer version of SSL
- HTTPS uses SSL certificate
- Public SSL certificates are issued by Certificate Authorities (CA)

## Load Balancer - SSL Certificates

- Load balancers use X.509 type of certificates (SSL/TLS server certificates).
- You can manage and renew certificates through:
  - ACM (AWS Certificate Manager)
  - Uploaded manually

## Server Name Indication Protocol (SNI)

- SNI enables servers of loading multiple SSL certificates onto one web server (to serve multiple websites)
- Clients can use SNI protocol to specify the hostname they want to reach
- The following supports SNI:
  - ALB
  - NLB
  - CloudFront
