[Back](./AWS.md)

# CloudFront

## General

- CloudFront is Content Delivery Network (CDN) service
- 216 Points of Presence Globally (edge locations)
- Content is cached at the edge locations
- DDoS protection (because workdwide)
- CloudFront is a global Service

## Origins (Content Sources)

- ALB
  - ALB SG must allow **public IP** of Edge Locations
  - EC2 Instances SG group behined the ALB may remain private
- EC2 instance
  - EC2 Instances SG must allow **public IP** of Edge Locations
- S3
  - Security with CloudFront **Origin Access Control (OAC)** with S3 bucket remaining private
    - old version was called Origin Access Identity (OAI)
  - Must modify the S3 bucket policy on the S3 bucket to accept access from CloudFront
  - CloudFront can be used as an ingress (to upload files to S3)
- Any **HTTP** backend you want

## Using CloudFront vs S3 Cross Region Replication

| CloudFront                  | S3 Cross Region Replication                |
| --------------------------- | ------------------------------------------ |
| Global Edge Network         | Must be set up for each region             |
| Files are cached with a TTL | Files are updated in near real-time        |
| Great for static content    | Great for dynamic content                  |
| Read/Write                  | Replication in another region is Read only |

## CloudFront Caching

- Control the TTL: 0 sec to 1 year (default 1 day)
- The cache lives at the CloudFront **Edge Location**
- You want to maximize the Cache hit ratio to minimize requests to the origin

### Cache key

- A unique identifier for every object in the cache
- By default, consists of hostname + resource portion of the URL, ex:
  - Hostname: `mywebsite.com`
  - Resource portion of the URL: `/content/stories/example-story.html`

### Cache Policy

- If you have an application that serves up content that varies based on user, device, language, location, you can **add other values to the Cache key**: HTTP headers, cookies, query strings, using **CloudFront Cache Policies**
- Cache based on (caching decisions):

  - HTTP Headers
  - Cookies
  - Query Strings

- **All HTTP headers, cookies, and query strings that you include in the Cache Key are automatically included in origin requests**

### Origin Request Policy

- Specify values that you want to include in request to the origin **without including the values in the Cache Key**
- You can include:
  - HTTP Headers
  - Cookies
  - Query Strings

### Cache Invalidation

- You can force an entire or partial cache refresh (so bypassing the TTL) by performing a CloudFront Invalidation
- You can invalidate all files (\*) or a special path (/images/\*)

### Cache Behaviors (Path patterns)

- You can use path pattern to route to different origin based on the content type
- Route to different kind of origins based on the content type or path pattern:
  - `/images/*` => Origin is S3 bucket
  - `/api/*` => Origin is an ALB
- When adding additional Cache Behaviors, the Default Cache Behavior is always the last to be processed and is always `/*`

## CloudFront Signed URL / Signed Cookies

- Signed URL = access to individual files
- Signed Cookie = access to multiple files
- Use case: You want to distribute **paid** shared content to premium users over the world

## CloudFront Signed URL / Signed Cookie Process

- Two types of signers:
  - **Trusted key group** (recommended)
    - Can leverage API's to create and rotate keys
  - An AWS Account that contains a CloudFront Key Pair (not recommended)
    - Need to manage keys using the root account and the AWS console
- You generate your own public / private key
  - The private key is used by your application (ex: EC2) to sign URLs
  - The public key (uploaded) is used by CloudFront to verify URL's

## CloudFront - Geo Restriction

- You can restrict who can access your distribution
  - **Allowlist:** Allow your users to access your content only if they're in one of the countries on a list of approved countries
  - **Blocklist:** Prevent your users from accessing your content if they're in one of the countries on a list of banned countries
- Use case: Copyright Laws to control access to content

## CloudFront - Price Classes

- You can reduce the number of **edge locations** for **cost reduction**
- Three price classes:
  1. Price Class All
  2. Price Class 200: most regions, but excludes the expensive ones
  3. Price Class 100: only the least expensive regions

## CloudFront - Origin Groups

- Origin Group: one primary and one secondary origin within a group
- If the primary origin fails, the second one is used
- To increase high-availability and do failover

## CloudFront - Field Level Encryption

- Uses **asymmetric** encryption: Sensitive information are encrypted at the edge close to user (using public key), request is decrypted at the server (using private key)

## CloudFront - Real Time Logs

- Get real-time requests recieved by CloudFront sent to **Kinesis Data Streams**
- Monitor, analyze, and take actions based on content delivery performance
- Real time logs allows you to choose:
  - Sampling Rate: percentage of requests for which you want to receive (if you are having lots of traffic but want to recieve a part of it)
  - Specific Cache Behaviours (path patterns)