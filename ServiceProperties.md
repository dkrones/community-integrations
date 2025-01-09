## What are service properties?

In our microservice architecture, detailed metadata is crucial for effective management, ownership clarity, and operational efficiency. This document outlines the attributes tracked for each microservice and how to manage them using YAML configuration files.

Fields can be managed via the OpsLevel user interface but are preferably defined in a YAML file (`opslevel.yml`) in the service's repository. Attributes not applicable to a service can be set to `null` or "None" to indicate their omission. Leaving a field empty implies it has not yet been evaluated.

## Which service properties do we track?

### General Summary
These attributes provide an overview of the service.

- **Name**  
  *The unique identifier for the service.*  
  *Alias Required*: Use the `name` attribute in OpsLevel YAML.

- **Description**  
  *A brief overview of the service's functionality and purpose.*  
  Relevant information includes the primary function, key users, and notable dependencies.

**Domain / System**  
  *The broader domain or system to which the service belongs.*  
  This attribute will receive its own detailed documentation later.

- **Lifecycle Stage**  
  *The current phase of the service, such as development, staging, or production.*  
  Lifecycle stages will be detailed in a dedicated document later.  
  *Alias Required*: Use lifecycle aliases from the OpsLevel Account page.

- **Tier**  
  *The service's criticality and support level.*  
  Refer to the detailed documentation in the `TierLevel.md` file.  
  *Alias Required*: Use the tier alias from OpsLevel.

### Ownership
The primary importance of our service catalogue is to have transparency on ownership. We track which team formally owns a service and in more detail the team's confidence in ownership.

- **Owner**  
  *The team or individual responsible for the service.*  
    
  *Alias Required*: Use the team alias from the OpsLevel Team page.

- **Ownership Confidence**  
*Reflects the team's confidence in their ownership of the service. A service that was recently handed over to team and is still faily unknown will have low confidence, while a service the team is actively working on in past months will have a high confidence.*  
Example: High.

### Development & Configuration
These attributes aid in understanding and managing the service's development and configuration.

- **Language**  
  *The programming language(s) used to implement the service.*  
  Example: Python.

- **Framework**  
  *The framework(s) utilized in the service's development.*  
  Example: Django.

- **Service Configuration**  
  *Describes service-specific parameters required for correct behavior.*  
  Example: default.

### Infrastructure Components
These attributes describe the required infrastructure and environment of the service.

- **Hosting**  
  *Identifies the hosting environment for the service.*  
  Example: GCP, Kubernetes, Webshop Cluster.  
  *Can be set to null.*

- **Database**  
  *Indicates the type and version of the database used by the service.*  
  Example: PostgreSQL.  
  *Can be set to null.*

- **Cache**  
  *Specifies the caching mechanism employed by the service.*  
  Example: Redis.  
  *Can be set to null.*

- **Message Queue Cluster**  
  *Defines the message queue usage, detailing producer/consumer roles and the cluster.*  
  Example: RMQ Webshop, Producer.  
  *Can be set to null.*

- **Storage**  
  *Lists the storage mechanisms used by the service.*  
  Example: S3 Bucket.  
  *Can be set to null.*

- **Static Content Hosting**  
  *Indicates how static content is hosted.*  
  Example: Akamai / Celum (cdn.shop-apotheke.com).  
  *Can be set to null.*

### Security & Accessibility
These attributes ensure secure access and data handling.

- **Authentication**  
  *Lists the authentication mechanisms offered by the service that are used to verify user or service identities.*  
  Example: User to Service (OIDC).  

- **Authorization**  
  *Describes the methods of access control used within the service.*  
  Example: Role-Based Access.  

- **Data Classification**  
  *Specifies the type of data handled by the service, categorized by sensitivity and legal requirements.*  
  Example: Customer Personally Identifiable Information (PII).  

- **Public Endpoint**  
  *If publicly available, indicates publicly accessible endpoints for the service, allowing identification of externally exposed functionality.*  
  Example: example.com/api/auth.  
  *Pattern*: `^(?:[a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}(?:/[^\s]*)?$`.  
  *Can be set to null.*


## How to track service properties?

Attributes are managed using a YAML file named `opslevel.yml` in the service's repository. Below is an example configuration:

```yaml
---
version: 1
component:
  name: Lorem Payment Gateway
  description: Allows users to add/remove products in their virtual shopping carts
    prior to placing an order.

  lifecycle:
  tier: tier_1
  owner: sec_k8s_team_name
  system:
  language: Java
  framework: Spring Boot

  properties:
    authentication:
    - User to Service (via CAS, JWT)
    authorization:
    - Single User Access
    cache:
    - None
    data_classification:
    - Customer Personally Identifiable Information (PII)
    - Customer Payment Data
    database:
    - MongoDB, 8.0+
    hosting:
    - GCP, Kubernetes, Webshop Cluster
    message_queue_cluster:
    - None
    messaging:
    - None
    ownership_confidence: High
    public_endpoint:
    service_configuration:
    static_content_hosting:
    - None
    storage:
    - None
    type: Backend

```

Ensure all aliases are retrieved from OpsLevel to maintain consistency.
