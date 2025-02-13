# Runbook structure example

The checked headlines go down to the second level ``##``. This is based on an example from the SWAP service.
Feel free to reach out with feedback and questions: [teams channel OpsLevel community](https://teams.microsoft.com/l/channel/19%3A1dbc01da7db443729a5ef523012efc14%40thread.tacv2/OpsLevel%20Community?groupId=29dbccf8-51ea-4d19-955b-4e165dd8d36f&tenantId=215a0c23-07f8-494c-9f75-1a9a8e0c716b)

# Runbook: SWAP-Service

## Overview

This runbook provides instructions for operating, deploying, and troubleshooting the SWAP-Service.

---

## Prerequisites

Ensure the following are installed and configured:

- **Java Development Kit (JDK)**: Version 17 or higher.
- **Maven**: Version 3.8+.
- **Git**: Version control system.
- **Docker**: Container deployment.

## GitHub Registry Setup

### Maven Dependencies from GitHub Packages

1. Configure `~/.m2/settings.xml` to include authentication for GitHub Packages:
   ```xml
   <servers>
       <server>
           <id>github</id>
           <username>YOUR_GITHUB_USERNAME</username>
           <password>YOUR_PERSONAL_ACCESS_TOKEN</password>
       </server>
   </servers>
   ```

---

## Application Details

- **Name**: swap-service
- **Primary Function**: SWAP-Service (Substitution With Accepted Pharmaceuticals) - substitutes products for an
  equivalent product.
- **Framework**: Spring Boot (version 3.4).
- **Port**: 8080 (default).
- **Dependencies**:
    - **Docker**:
        - Active docker daemon.
    - **MySQL Database**:
        - Used for storing ABDA data.
        - Connection configured in `application.yml`.
    - **S3 Server**:
        - Used to store audit logs.
        - Connection details configured in `application.yml`.
- **Optional Dependenices**:
    - **FTP Server**:
        - Used for retrieving biweekly ABDA updates.
        - Connection details configured in `application.yml`.
    - **RabbitMQ**:
      - Provides a messaging system for transmitting Pharma Data Catalogue messages to SWAP.
      - Connections details configured in `application.yml`

---

## Starting the Application

### Local Environment

1. Clone the repository:
   ```bash
   git clone https://github.com/RedTecLab/swap-service/
   cd swap-service
   ```

2. Build the application:
   ```bash
   make localbuild
   ```

3. Run the application:
   ```bash
   make support
   make start
   ```
   Docker Compose will be used to launch all dependencies.


4. Access the endpoint:
    - Open a browser to visit `http://localhost:8080/swagger-ui/index.html`.
    - **Important:** On the production environment this endpoint is disabled. Please refer to the contract defined in
      our central *api*
      project at `https://github.com/RedTecLab/api`.

---

## Deploying the Application

### CI/CD Workflow Overview

This application uses **CircleCI** for continuous integration and deployment. Argo CD is triggered automatically as part
of the CircleCI pipeline to manage Kubernetes deployments.

---

### CircleCI Build and Deployment Process

1. **Triggering a Deployment**:
    - Push changes to the `main` branch or create a pull request (PR). This automatically starts the CircleCI pipeline
      based on the `.circleci/config.yml`.

2. **Pipeline Stages**:
    - **Build and Test**:
        - Compiles the application and runs unit tests using Maven.
    - **Docker Image Build**:
        - Builds a Docker image for the application.
    - **API Contract Build**:
        - Builds the API contract for SWAP.
    - **Publish Docker Image**:
        - Publishes the Docker image to the GitHub Container Registry (GHCR) or another specified Docker registry.
    - **Update Kubernetes Manifests**:
        - Updates the image tag in the Kubernetes manifests stored in the configuration repository (Helm).
    - **Deployment with Argo CD**:
        - Commits updated manifests to the configuration repository.
        - Triggers Argo CD to sync and deploy the application to the target environment.

3. **Artifact Details**:
    - Docker images are tagged as:
        - `ghcr.io/gke-utility/swap-service:<commit-hash & build hash>` and `latest`.

---

## Environment Variables
- Could be skipped with the service configuration field in OpsLevel 

---

### Monitoring Deployment

1. **CircleCI Pipeline**:
    - Monitor the pipeline in the CircleCI dashboard:
        - Ensure all jobs (build, test, Docker push, manifest update, and Argo CD sync) complete successfully.

2. **Argo CD Application Status**:
    - Login to the Argo CD UI at `https://cd-prod.sae.systems/` and verify the application
      status ([Prod](https://cd-prod.sae.systems/applications/swap-service-prod), [Ref](https://cd-prod.sae.systems/applications/swap-service-ref)):
        - **Healthy**: Deployment succeeded.
        - **Progressing**: Deployment is in progress.
        - **Degraded**: Deployment failed (requires troubleshooting).

3. **Logs**:
    - For pipeline logs, use the CircleCI dashboard.
    - For application logs, access Kubernetes:
      ```bash
      kubectl -n swap-service get pods
      kubectl logs -n swap-service <pod-name>
      ```
    - or visit `https://telemetry.ref-sa-tech.io/app/discover#/view/6942c740-1486-11ec-a36b-912392e07e6b`
        - pay attention to the environment filters to detect issues

---

### Troubleshooting Deployment

#### Pipeline Fails in CircleCI

- **Error**: Failure during build, test, or manifest update stages.
- **Resolution**:
    1. Review the CircleCI logs to identify the failure stage.
    2. If the issue is in Docker image build or push:
        - Verify that the Docker registry credentials are correctly configured in CircleCI.
    3. If the issue is in manifest updates:
        - Check for syntax errors in Kubernetes manifests or Helm charts.

#### Argo CD Sync Issues

- **Error**: Argo CD reports `OutOfSync` status after pipeline completion.
- **Resolution**:
    1. Ensure the correct image tag was updated in the manifests by CircleCI.
    2. Check the `argocd-application-controller` logs for more details:
       ```bash
       kubectl logs -n argocd <application-controller-pod>
       ```

#### Deployment Fails in Kubernetes

- **Error**: Application pods fail to start or show errors.
- **Resolution**:
    1. Check pod logs for errors:
       ```bash
       kubectl logs -n swap-service <pod-name>
       ```
    2. Inspect the events for the namespace:
       ```bash
       kubectl get events -n swap-service
       ```
    3. Verify resource configurations (e.g., CPU, memory limits) in the manifests.
    4. Further information can be found on the
       GCP [GCP Ref](https://console.cloud.google.com/kubernetes/deployment/europe-west3/shared-reference/swap-service/swap-service/overview)
       and [GCP Prod](https://console.cloud.google.com/kubernetes/deployment/europe-west3/shared-production-v2/swap-service/swap-service/overview)

---

### Notes

- **CircleCI Config**: The build and deployment workflow is defined in `.circleci/config.yml`.
- **Argo CD Config**: Kubernetes manifests or Helm charts are located in the `.deployment` folder.

---
## Monitoring and Logging

### Logs

- Access logs in the console during runtime.
- In Docker:
   ```bash
   docker logs <container-id>
   ```

### Health Checks

- Verify application health:
    - Default actuator endpoint: `http://localhost:8080/actuator/health`.
- Health issues on the GCP should cause events

---

## Links to alerts

---

## Troubleshooting

### Common Production Issues

#### Service not scalable: ImagePullBackOff

- Step 1: Verify if the image is not available any more (might have been deleted by cleanup job)
- Step 2: Trigger the build pipeline again to create a new image (e.g. by adding a space)
- Step 3: Pray

### Common Local Issues

#### Port Already in Use

- **Error**: `java.net.BindException: Address already in use`
- **Resolution**: Check for processes using port 8080 and terminate them:
   ```bash
   lsof -i :8080
   kill <process-id>
   ```

#### Dependency Issues during Build

- **Error**: Build failure due to missing dependencies.
- **Resolution**: Ensure all dependencies are listed correctly in `pom.xml` and run:
   ```bash
   mvn dependency:resolve
   ```
- **Alternative**: Make sure your Github token is set correctly in the `.m2/settings.xml` and rerun maven.

#### Application Not Responding

- **Error**: Unable to access `http://localhost:8080/swagger-ui.html`.
- **Resolution**:
    1. Verify the application is running:
       ```bash
       ps -ef | grep java
       ```
    2. Check logs for errors.

---

#### MySQL Connection Failure

- **Error**: `Cannot connect to MySQL server`.

- **Resolution**:
    1. Verify the MySQL server is running:
        - Locally use an equivalent of
            - ```bash
              systemctl status mysql
              ```
        - Docker
            - ```bash
              docker ps
              ```
    2. Check database credentials in `application.yml`.
    3. Ensure the database host and port are accessible.

#### FTP Connection Failure

- **Error**: `Failed to connect to FTP server`.
- **Resolution**:
    1. Verify the FTP server is running and accessible.
    2. Check credentials and host details in `application.yml`.
    3. Test connection manually:
       ```bash
       ftp localhost:22
       ```

## Stopping the Application

### Locally

- Terminate the process running the application:
   ```bash
   kill <process-id>
   ```

### Docker

- Stop the container:
   ```bash
   docker stop <container-id>
   ```

---

## Escalation

### On-Call contacts
primary contact:
secondary contact: Area Engineering Manager

### Communication channels
-> Available in OpsLevel
-> Stakeholders notifications:
-> Incident reports:

## Additional Notes

- **Default Configuration File**: `application.yml`
- **Reference**: `application-reference.yml`
- **Production**: `application-production.yml`
- Modify the application port or other settings in the `application.yml` file located under `src/main/resources`.
- Further settings are kept within the Helm charts in `.deployment/dev-values-shared*.yaml`.

