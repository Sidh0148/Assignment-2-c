# Assignment-2-c
# Best Buy Cloud-Native Application

This is a cloud-native application developed for Best Buy, following microservices architecture principles. The app includes customer-facing and employee-facing functionalities, backed by AI for product descriptions and image generation.

## Features:
- **Store-Front**: Customer-facing app for browsing and placing orders.
- **Store-Admin**: Employee-facing app for managing products and viewing orders.
- **Order-Service**: Handles order creation and queues orders in rabbitmq.
- **Product-Service**: Manages product data.
- **Makeline-Service**: Processes orders from rabbitmq.
- **AI-Service**: Generates product descriptions and images using GPT-4 and DALL-E.
- **MongoDB**: Stores product and order data.

## Architecture Diagram
![Application Architecture](![Untitled diagram-2024-12-12-214751](https://github.com/user-attachments/assets/eb862d98-2fb3-4e4e-913d-b75520351cf0))


### Architecture Explanation:
1. **Store-Front**: Sends customer orders to the `Order-Service`.
2. **Order-Service**: Pushes orders to Azure Service Bus.
3. **Makeline-Service**: Processes orders from the queue and updates the database.
4. **Product-Service**: Handles product CRUD operations.
5. **AI-Service**: Generates AI-powered product descriptions and images.
6. **MongoDB**: Acts as the persistent store for product and order data.
7. **Kubernetes**: Manages all services using ConfigMaps, Secrets, and StatefulSets.

   ## Deployment Instructions

### Prerequisites:
- Kubernetes Cluster (AKS or similar).
- Azure Service Bus setup.
- Docker installed and configured.

### Steps:
1. Clone the repository:
   ```bash
   git clone <repository-link>
   cd <repository-folder>


---

### **4. Microservices Table**
Provide a table linking each microservice to its repository.

#### Example:
markdown
## Microservice Repositories

| Service         | Repository Link                   |
|-----------------|-----------------------------------|
| Store-Front     | [GitHub](https://github.com/Sidh0148/store-admin-L8)  |
| Store-Admin     | [GitHub](https://github.com/Sidh0148/store-front-L9)  |
| Order-Service   | [GitHub](https://github.com/Sidh0148/order-service-L8)  |
| Product-Service | [GitHub](https://github.com/Sidh0148/product-service-L8)  |
| Makeline-Service| [GitHub](https://github.com/Sidh0148/makeline-service-L8)  |
| AI-Service      | [GitHub](https://github.com/Sidh0148/ai-service-L8)  |

## Docker Images

| Service         | Docker Image Link                |
|-----------------|----------------------------------|
| Store-Front     | [Docker Hub](https://hub.docker.com/repository/docker/aakashsidhu/store-front/general) |
| Store-Admin     | [Docker Hub](https://hub.docker.com/repository/docker/aakashsidhu/store-admin/general) |
| Order-Service   | [Docker Hub](https://hub.docker.com/repository/docker/aakashsidhu/order-service/general) |
| Product-Service | [Docker Hub](https://hub.docker.com/repository/docker/aakashsidhu/product-service/general) |
| Makeline-Service| [Docker Hub](https://hub.docker.com/repository/docker/aakashsidhu/makeline-service/general) |
| AI-Service      | [Docker Hub](https://hub.docker.com/repository/docker/aakashsidhu/ai-service/general) |

## Deployment Files
| Service         |  .yaml Link                |
|-----------------|----------------------------------|
| Store-Front     | [GitHub](https://github.com/Sidh0148/Deployments/blob/main/store-front.yaml) |
| Store-Admin     | [GitHub](https://github.com/Sidh0148/Deployments/blob/main/store-admin.yaml) |
| Order-Service   | [GitHub](https://github.com/Sidh0148/Deployments/blob/main/order-service.yaml) |
| Product-Service | [GitHub](https://github.com/Sidh0148/Deployments/blob/main/order-service.yaml) |
| Makeline-Service| [GitHub](https://github.com/Sidh0148/Deployments/blob/main/makeline-service.yaml) |
| AI-Service      | [GitHub](https://github.com/Sidh0148/Deployments/blob/main/ai-service.yaml) |

