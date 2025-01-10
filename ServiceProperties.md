## What are service properties?

In our microservice architecture, detailed metadata is crucial for effective management, ownership clarity, and operational efficiency. This document outlines the attributes tracked for each microservice and how to manage them using YAML configuration files.

Fields can be managed via the OpsLevel user interface but are preferably defined in a YAML file (`opslevel.yml`) in the service's repository. Attributes not applicable to a service can be set to `null` or "None" to indicate their omission. Leaving a field empty implies it has not yet been evaluated.

## Which service properties do we track?

### General Information
These attributes provide an overview of the service.

- **Name**  
  The display name for each service. Should be easily identifiable for other teams.

- **Description**  
  A brief overview of the service's functionality and purpose. Aim for a maximum of 150-200 characters. Do not repeat detailed information that is captured in different places, such as technologies used or service dependencies.

- **Domain / System**  
  The broader domain or system to which the service belongs.\
  *Note: This attribute will receive its own detailed documentation later.*

- **Lifecycle Stage**  
  The current phase in development lifecycle of the service, such as concept, development, staging or production. *Alias Required*
  - <details>
      <summary>Supported Values</summary>

    - `concept_design` - he service is in the planning phase, with requirements, designs and architecture being defined.
    - `proof_of_concept` - A small-scale version of the service is being tested internally to validate its technical feasibility.
    - `in_development` - The service is actively being built, tested, and prepared for release.
    - `available` - The service is fully operational and accessible to users, with active support and ongoing updates.
    - `sunsetting` - The service is being phased out, with limited support and a plan for decommissioning.
    - `retired` - The service has been decommissioned and is no longer available or supported.
    
    </details>

- **Tier**  
  The service's criticality and support level.  Refer to the detailed documentation in the `TierLevel.md` file. *Alias Required*
  - <details>
      <summary>Supported Values</summary>

    - `tier_1` - Mission-critical services. Failure results in immediate and significant impact on revenue, customer trust, or core operational processes.
    - `tier_2` - Customer-facing services. If disrupted, they degrade the customer experience. While there may be an impact on revenue, operations, or trust, it is more limited compared to Tier 1.
    - `tier_3` - Delay-tolerant services, or internal services. Downtime does not severely or immediately impact the customer experience or critical operations, though it may reduce internal productivity.
    - `tier_4` - Other, or low impact services. Failure does not have an immediate or noticeable effect.
        
    </details>

### Ownership
The primary importance of our service catalogue is to have transparency on ownership. We track which team formally owns a service and in more detail the team's confidence in ownership.

- **Owner**  
  The team responsible for the service.  *Alias Required: Use the team alias from the OpsLevel Team page.*

- **Ownership Confidence**  
Reflects the team's confidence in their ownership of the service. A service that was recently handed over to team and is still faily unknown will have low confidence, while a service the team is actively working on in past months will have a high confidence.
  - <details>
    <summary>Supported Values</summary>

    - `High` - The team regularly works on the service, can troubleshoot problems quickly, and has a solid understanding of its code and technical architecture. They are well-equipped to handle changes, dependencies, and operational challenges with minimal disruption.
    - `Medium` - The team occasionally works on the service and can troubleshoot common issues, but there may be gaps in their understanding of its code or technical architecture. While they can manage day-to-day operations, more complex problems require additional time.
    - `Low` - The team rarely works on the service and struggles to troubleshoot issues or understand its code and technical architecture. Operational challenges or changes lead to delays.
    
</details>

### Development & Configuration
These attributes aid in understanding and managing the service's development and configuration.

- **Language**  
  The primary programming language used to implement the service.
  *Example: Java.*

- **Framework**  
  The framewor utilized in the service's development.
  *Example: Spring Boot.*

- **Service Configuration**  
  Describes implementation of service-specific parameters required for correct behavior. Free-text field shall be used to describe whether parameters are purely in code under version control, if these are changed during run-time through external services or any other additional methods that would for correct behaviour of service.

### Infrastructure Components
These attributes describe the required infrastructure and environment of the service.

- **Hosting**  
  Identifies the hosting environment for the service.
  *Can be set to null.*
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `GCP, Kubernetes, Webshop Cluster`
    - `GCP, Kubernetes, shared Cluster`
    - `Hybrid`
    - `Azure`
    - `ACC, Bare Metal`
    - `Venlo, Bare Metal`

  </details>


- **Database**  
  Indicates the type and version of the database used by the service.
  *Can be set to null.*
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `MongoDB, 8.0+`
    - `MongoDB, 7.0+`
    - `MongoDB, 6.0+`
    - `MySQL`
    - `PostgreSQL`

  </details>


- **Cache**  
  Specifies the caching mechanism employed by the service.
  *Can be set to null.*
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `Redis`
    
  </details>


- **Message Queue Cluster**  
  Defines the message queue usage, detailing producer/consumer roles and the cluster.
  *Can be set to null.*
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `RMQ Webshop, Consumer`
    - `RMQ Webshop, Producer`
    - `RMQ Venlo, Consumer`
    - `RMQ Venlo, Producer`

  </details>


- **Storage**  
  Lists the storage mechanisms used by the service.
  *Can be set to null.*
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `S3 Bucket`
    - `GCP Bucket`
    - `FTP, Consumer`
    - `FTP, Producer`

  </details>

- **Static Content Hosting**  
  Indicates how static content is hosted.
  *Can be set to null.*
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `Mediaserver (static.shop-apotheke.com)`
    - `Akamai / Celum (cdn.shop-apotheke.com)`
    - `Adserver. Kevel CDN (c.sa-tech.de)`

  </details>



### Security & Accessibility
These attributes describe how basic security practices and sensitivity of data being handled.

- **Authentication**  
  Lists the authentication mechanisms offered by the service that are used to verify user or service identities.
*Can be set to null.*
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `User to Service (via CAS, JWT)`
    - `User to Service (SSO/SAML)`
    - `Service to Service (API Key)`
    - `Service to Service (Basic Auth)`
    - `Service to Service (OAuth 2.0)`

  </details>


- **Authorization**  
  Describes the methods of access control used within the service.
  *Can be set to null.* 
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `Single User Access` - All (authenticated) users have same level of privileges.
    - `Route-Based Access` - Depending on route, (authenticated) user have the same level of privileges.
    - `Role-Based Access` - Privileges are granted based on role of authenticated users.

  </details>


- **Data Classification**  
  Specifies the type of data handled by the service, categorized by sensitivity and legal requirements. Multiple values can be selected.
*Can be set to null.*
  - <details>
    <summary>Supported Values</summary>

    - `None`
    - `Public Data`
    - `Anonymized / Aggregated Data`
    - `Operational Data (Logs, Metrics)`
    - `Pseudonymized Data`
    - `Customer Personally Identifiable Information (PII)`
    - `Customer Payment Data`
    - `Customer Health Data`
    - `Custom Classification`

  </details>


- **Public Endpoint**  
  If service is publicly available, indicates publicly accessible endpoints for the service, allowing identification of externally exposed functionality.
  Example: example.com/api/auth.


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
