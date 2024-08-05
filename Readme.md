# Claims Processing System

*Disclaimer: This document uses fictional names for the project and client for illustrative and educational use only. Any resemblance to real entities is coincidental.*

## Introduction

The Claims Processing System for Statewide Insurance Group is a centralized platform designed to manage and process insurance claims efficiently.This system aims to streamline the claims handling process by consolidating data from various internal and external sources such as client databases and insurance databases. The system provides real-time updates on claim status, automates the evaluation of claims through preset rules, and offers detailed reporting features for insurance agents and clients. The design is based on a monolithic architecture to facilitate ease of deployment, simpler scalability options, and straightforward maintenance.

## Project Architecture

### Client Side
- **Technology:** Angular 10
- **Team:** Separate frontend developers team

### Monolithic Backend
- **Claims Processing Module:** Manages the validation, processing, and status updates of insurance claims.
- **User Management Module:** Handles authentication, user account management, and role-based access control.
- **Reporting Module:** Generates detailed claim reports and analytics.

### Database
- **Technology:** MySQL
- **Purpose:** Robust data management and complex queries required by insurance data handling.

### Cache Mechanism
- **Technology:** Redis
- **Purpose:** In-memory cache solution for storing frequently accessed data, such as insurance policy details, user profiles, or claim statuses.

### Logging Tools
- **Log4j2:** Provides powerful logging capabilities for Java applications.
- **Splunk:** Platform for searching, analyzing, and visualizing machine-generated data, including logs, in real-time.

### Security
- **OAuth:** Implemented for secure access control, allowing users to sign into the app using accounts from different places.

### Deployment and Operations
- **Containerization:** Using Docker to containerize the application, making it easy to deploy and scale.
- **Orchestration:** Using Kubernetes to manage containers, handle deployment, scaling, and operation of application containers across clusters of hosts.
- **CI/CD:** Using Jenkins for Continuous Integration and Continuous Deployment, automating the build, test, and deployment processes.
- **Git Collaboration Tool:** Using GitLab

### Messaging System
- **Technology:** Kafka
- **Purpose:** Real-time updates, such as notifications about the status of claims (received, processed, approved, or denied).

## Project Flow

### Step 1: Claim Submission and Authentication
- **Action:** An insurance agent logs into the system to submit a new claim.
- **Process:** 
  - User management module handles the login request and authenticates user credentials.
  - Upon successful authentication, it generates an access token (using OAuth) for secure sessions.
  - For any request made by an agent, the same access token is sent to the server for authorization.

### Step 2: Claim Validation and Processing
- **Action:** Automatic validation and processing of the submitted claim.
- **Process:**
  - Claims Processing Module checks the validity of the claim based on predefined rules and past data.
  - Valid claims are forwarded for further processing and calculation of settlements.

### Step 3: Reporting and Feedback
- **Action:** Generation of final claim reports and feedback to stakeholders.
- **Process:**
  - Reporting Module compiles detailed reports on the claim, including decision reasoning and settlement amounts.
  - Reports are made available to agents and can be sent to clients via email.

### Common Activities
- Throughout all steps, all data transactions and user actions are logged and can be seen on Splunk.
- Kafka is used for operations requiring real-time data updating, such as the status of claims.

## Use of Kafka in the Project
- **Description:** Kafka handles real-time notifications and communications between different parts of the system.
- **Implementation:** Kafka topics segregate different types of messages, such as claim updates, enabling asynchronous processing and reducing the load on the main application server.

## Use of Log4j2
- **Features:** Asynchronous logging, structured logging, and dynamic configuration.
- **Configuration:**
  - Excluded the default logging dependency (Spring Boot Starter Logging).
  - Added dependencies for Log4j2 in the POM file.
  - Configured the YML file in the resources.

## Integration with Splunk
- **Log Forwarding:** Log4j2 sends logs directly to Splunk using Splunk Universal Forwarder.
- **Metadata:** Additional metadata in log messages provides context for log analysis in Splunk.

## Use of Redis Cache
- **Purpose:** Enhances efficiency by storing frequently accessed data, reducing the need to repeatedly query the database.
- **Implementation:**
  - **Claim Status Updates:** Stores the latest statuses of claims for real-time updates.
  - **Frequently Accessed Reports:** Caches generated reports for quick access without regenerating them each time.

## CI/CD Pipeline
### Version Control System
- **Technology:** Git
- **Hosting:** GitHub or GitLab

### Continuous Integration Tools
- **Technology:** Jenkins
- **Configuration:** Managed by a Jenkinsfile

### Build Automation
- **Tools:** Maven or Gradle
- **Process:** Compilation, testing, and code analysis upon each commit

### Artifact Repository
- **Tools:** Nexus or Artifactory
- **Purpose:** Stores built files (e.g., JAR files) and keeps all versions of the software.

### Continuous Deployment
- **Containerization:** Docker
- **Orchestration:** Kubernetes
- **Deployment Strategies:** Blue-green deployments or canary releases for minimal disruption during updates.

## Data Layer
- **Technology:** MySQL

### Major Tables and Fields
- **Claims Table:**
  - `claim_id` (Primary Key)
  - `user_id` (Foreign Key)
  - `claim_date`
  - `claim_amount`
  - `claim_type` (e.g., auto, home, life)
  - `claim_status` (e.g., submitted, processed, approved, denied)
  - `last_updated`
- **Users Table:**
  - `user_id` (Primary Key)
  - `username`
  - `password_hash`
  - `email`
  - `role` (e.g., agent, adjuster, administrator)
  - `created_at`
  - `updated_at`
  - `last_login`
  - `status` (e.g., active, inactive)
- **Transactions Table:**
  - `transaction_id` (Primary Key)
  - `claim_id` (Foreign Key)
  - `transaction_date`
  - `amount`
  - `transaction_type` (e.g., payout, adjustment)
  - `description`
- **Audit Logs Table:**
  - `log_id` (Primary Key)
  - `action`
  - `timestamp`
  - `user_id` (Foreign Key)
  - `details` (description of the action taken, changes made, etc.)
- **Payment Details Table:**
  - `payment_id` (Primary Key)
  - `claim_id` (Foreign Key)
  - `payment_date`
  - `amount`
  - `payment_method` (e.g., check, bank transfer)
  - `status` (e.g., pending, completed)

## REST APIs

### Claims Management
- `POST /claims` - Submit a new insurance claim.
- `GET /claims/{claimId}` - Retrieve details about a specific claim using its ID.
- `PUT /claims/{claimId}` - Update an existing claim (e.g., change status or update claim details).
- `DELETE /claims/{claimId}` - Delete a specific claim.
- `GET /claims/user/{userId}` - Retrieve all claims submitted by a specific user.

### User Authentication and Management
- `POST /users/register` - Register a new user account.
- `POST /users/login` - Authenticate a user and return a JWT.
- `GET /users/{userId}` - Get details of a specific user.
- `PUT /users/{userId}` - Update user profile information.
- `DELETE /users/{userId}` - Deactivate or delete a user account.

### Reporting
- `GET /reports/claims/status` - Generate a report on claims based on their status (e.g., pending, approved, denied).
- `GET /reports/claims/summary` - Generate a summary report of claims over a specified period.

### Notifications and Real-time Updates
- `POST /notifications/subscribe` - Subscribe a user or device to real-time updates via WebSocket or other means.
- `POST /notifications/unsubscribe` - Unsubscribe from real-time notifications.

### Administrative and Support
- `GET /admin/logs` - Access system logs for maintenance and auditing purposes.