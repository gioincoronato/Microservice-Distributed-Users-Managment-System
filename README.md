#Distributed Microservices User Management System
A robust, containerized microservices architecture for managing user accounts and asynchronous notifications.

Built as a proof-of-concept to demonstrate modern backend patterns, this project integrates heterogeneous technologies including Python, Java, Flask, gRPC, REST APIs, MongoDB, and Message Brokers, all fully orchestrated with Docker.

Architecture Overview
The system is designed with scalability and decoupling in mind. The architecture consists of the following isolated components:
Test Client (Python): Simulates external HTTP requests to interact with the system.
API Gateway (Python/Flask): The single entry point for external REST/HTTP calls. It routes traffic and translates HTTP requests into gRPC calls for internal services.
User Service (Python/gRPC): The core business logic service. It handles high-performance gRPC requests from the Gateway, performs CRUD operations, and manages data persistence.
Database (MongoDB): A NoSQL database used by the User Service to store user documents.
Message Broker (Topic): Handles asynchronous event-driven communication (using STOMP pub / JMS sub). Whenever a user is successfully created, an event is published here.
Notification Service (Java): An independent background service acting as a subscriber. It listens to the message broker topic via JMS to trigger post-creation workflows (e.g., sending welcome emails/notifications).

```mermaid
graph TD
    Client[Client Python] -->|http| Gateway[API GATEWAY Flask]
    Gateway -->|status| Client
    
    Gateway -->|gRPC Request| UserService[User Service Py gRPC]
    UserService -->|gRPC Response ACK/ERR| Gateway
    
    UserService -->|insert| DB[(MongoDB)]
    DB -->|ACK/ERR| UserService
    
    UserService -->|STOMP pub if success| Topic{topic}
    Topic -->|JMS sub| NotifService[Notification SERVICE JAVA]
