# CafeCorp-Monolith-to-Microservices-Migration

## Project Overview

This project documents the end-to-end modernization of a legacy monolithic application, transforming it into a robust and scalable microservices architecture on AWS. The core challenge was to resolve performance and reliability issues by containerizing the application and implementing a fully automated CI/CD pipeline. The final solution enabled independent scaling of services and guaranteed zero-downtime deployments through a blue/green strategy, significantly enhancing the application's overall resilience and operational efficiency.

## Architecture

The architecture follows a microservices pattern, securely deployed within an **Amazon Virtual Private Cloud (VPC)**. The solution separates the application's functionality into two distinct, independently deployable services: a `customer` service and an `employee` service.

<img width="977" height="739" alt="image" src="https://github.com/user-attachments/assets/728d5027-9661-426c-b8e8-716a63b2466b" />

### Key Architectural Components

  * **Application Load Balancer (ALB):** Acts as the single entry point for all web traffic. The ALB is configured with path-based routing rules to direct requests to the appropriate target group. For example, requests to `/admin/*` are routed to the **`employee`** microservice, while all other requests are routed to the **`customer`** microservice.
  * **Amazon ECS (Elastic Container Service):** Manages the orchestration and lifecycle of the Docker containers. It runs two services, each with a task definition that specifies the container image, resource allocation (CPU/memory), and environment variables.
  * **Docker:** The application's runtime environment is containerized using Docker, ensuring portability and consistency across development, staging, and production environments.
  * **Amazon ECR (Elastic Container Registry):** Serves as a private Docker registry where all microservice images are securely stored and versioned.
  * **Amazon RDS (Relational Database Service):** A single managed MySQL instance provides the persistent data storage. All database connections are handled securely through environment variables passed to the containers, maintaining separation of concerns.

## Technical Stack

This project was built using a comprehensive set of modern cloud technologies and development practices.

| Category | Technologies | Description |
| :--- | :--- | :--- |
| **Cloud Platform** | AWS | The foundational cloud provider for all infrastructure. |
| **Containerization** | **Docker**, **ECR** | Containers are built with Docker and stored in a private ECR repository for secure image management. |
| **Application** | **Node.js** | The backend is built on a Node.js runtime, with separate codebases for the customer and employee microservices. |
| **Database** | **Amazon RDS (MySQL)** | A managed relational database that provides high availability and automatic backups. |
| **CI/CD** | **CodePipeline**, **CodeDeploy** | A complete CI/CD pipeline for automating the build, test, and deployment of updates. |
| **Networking** | **ALB**, **VPC** | The ALB intelligently routes traffic, while the VPC ensures all resources are in a secure, isolated network. |
| **Observability** | **CloudWatch** | Collects and analyzes logs from all services and provides performance metrics and alarms for health monitoring. |
| **Version Control** | **CodeCommit**, **Git** | The source code is version-controlled and hosted in a CodeCommit repository. |
| **Development** | **AWS Cloud9** | A cloud-based IDE used for writing, editing, and testing code, with pre-configured development tools. |

## CI/CD Pipeline & Deployment Strategy

The CI/CD pipeline is designed for efficiency and reliability, enabling a continuous delivery model.

**[Insert your CI/CD pipeline diagram here]**

### Pipeline Stages

1.  **Source Stage:** A developer pushes new code to the `dev` branch of the **CodeCommit** repository, which automatically triggers the pipeline.
2.  **Build Stage:** **CodePipeline** initiates a build job that references a `Dockerfile`. It builds a new Docker image for the updated microservice, tags it with a unique ID, and pushes it to **Amazon ECR**.
3.  **Deploy Stage:** **CodeDeploy** is triggered to execute a **blue/green deployment**.
      * **Blue/Green Deployment:** A new "green" ECS task set is provisioned with the new Docker image. The **ALB** then gradually shifts traffic to the "green" environment. Once a health check and validation period pass, all traffic is routed to the new task set. The old "blue" task set is then automatically de-provisioned, ensuring a seamless, zero-downtime release.

### Routing & Scalability

  * The **ALB** intelligently routes traffic based on URL paths, allowing for a single entry point while distributing load to the appropriate microservice.
  * **ECS** services are configured to **auto-scale** based on metrics like CPU utilization, ensuring that the application can handle increased traffic without manual intervention.

## Project Outcomes & Key Achievements

  * **Enhanced System Resilience:** The microservices design eliminates single points of failure. If one service encounters an issue, the other remains operational, preserving core functionality.
  * **Automated and Streamlined Workflow:** The CI/CD pipeline drastically reduces the time and effort required for deployments, moving from manual updates to a fully automated process.
  * **Guaranteed Zero-Downtime:** The blue/green deployment strategy ensures that users never experience service interruptions, providing a consistent and reliable user experience.
  * **Improved Scalability & Performance:** The ability to scale microservices independently allows for efficient resource allocation, preventing bottlenecks and optimizing performance for high-demand services.

## Getting Started

### Prerequisites

  * An AWS Account with appropriate IAM permissions.
  * Familiarity with the AWS Management Console and CLI.

### How to Deploy

1.  **Clone the Repository:**
    ```bash
    git clone https://github.com/your-username/your-repo.git
    ```
2.  **Create AWS Resources:** Use the AWS console or IaC tools (e.g., CloudFormation, Terraform) to provision the required resources (VPC, ECS Cluster, RDS, etc.).
3.  **Configure CI/CD:** Set up the CodePipeline with the correct CodeCommit repository and buildspec/appspec files.
4.  **Push Code:** Pushing code to the `dev` branch will automatically trigger the pipeline.
