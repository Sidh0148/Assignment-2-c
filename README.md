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

# Steps:
## Step 1: Clone the Algonquin Pet Store Repository

To begin, clone the [**Algonquin Pet Store (On Steroids)**](https://github.com/ramymohamed10/algonquin-pet-store-on-steroids) repository, which contains all necessary deployment files.

 **Review the Deployment Files**:
   - Navigate to the `Deployment Files` folder
   - This folder contains YAML files for deploying all necessary Kubernetes resources, including services, deployments, StatefulSets, ConfigMaps, and Secrets.


## Step 2: Set Up the AKS Cluster
Create an AKS cluster with two worker nodes for this exercise. For this step, you can refer to: [Lab 6](https://github.com/ramymohamed10/Lab6_24F_CST8915)

## Step 3: Set Up the AI Backing Services
To enable AI-generated product descriptions and image generation features, you will deploy the required **Azure OpenAI Services** for GPT-4 (text generation) and DALL-E 3 (image generation). This step is essential to configure the **AI Service** component in the Algonquin Pet Store application.

### Task 1: Create an Azure OpenAI Service Instance

1. **Navigate to Azure Portal**:
   - Go to the [Azure Portal](https://portal.azure.com/).

2. **Create a Resource**:
   - Select **Create a Resource** from the Azure portal dashboard.
   - Search for **Azure OpenAI** in the marketplace.

3. **Set Up the Azure OpenAI Resource**:
   - Choose the **East US** region for deployment to ensure capacity for GPT-4 and DALL-E 3 models.
   - Fill in the required details:
     - Resource group: Use an existing one or create a new group.
     - Pricing tier: Select `Standard`.

4. **Deploy the Resource**:
   - Click **Review + Create** and then **Create** to deploy the Azure OpenAI service.

---

### Task 2: Deploy the GPT-4 and DALL-E 3 Models

1. **Access the Azure OpenAI Resource**:
   - Navigate to the Azure OpenAI resource you just created.

2. **Deploy GPT-4**:
   - Go to the **Model Deployments** section and click **Add Deployment**.
   - Choose **GPT-4** as the model and provide a deployment name (e.g., `gpt-4-deployment`).
   - Set the deployment configuration as required and deploy the model.

3. **Deploy DALL-E 3**:
   - Repeat the same process to deploy **DALL-E 3**.
   - Use a descriptive deployment name (e.g., `dalle-3-deployment`).

4. **Note Configuration Details**:
   - Once deployed, note down the following details for each model:
     - Deployment Name
     - Endpoint URL

---

### Task 3: Retrieve and Configure API Keys

1. **Get API Keys**:
   - Go to the **Keys and Endpoints** section of your Azure OpenAI resource.
   - Copy the **API Key (API key 1)** and **Endpoint URL**.

2. **Base64 Encode the API Key**:
   - Use the following command to Base64 encode your API key:
     ```bash
     echo -n "<your-api-key>" | base64
     ```
   - Replace `<your-api-key>` with your actual API key.

---

### Task 4: Update AI Service Deployment Configuration in the `Deployment Files` folder.
1. **Modify Secretes YAML**:
   - Edit the `secrets.yaml` file.
   - Replace `OPENAI_API_KEY` placeholder with the Base64-encoded value of the `API_KEY`. 
2. **Modify Deployment YAML**:
   - Edit the `aps-all-in-one.yaml` file.
   - Replace the placeholders with the configurations you retrieved:
     - `AZURE_OPENAI_DEPLOYMENT_NAME`: Enter the deployment name for GPT-4.
     - `AZURE_OPENAI_ENDPOINT`: Enter the endpoint URL for the GPT-4 deployment.
     - `AZURE_OPENAI_DALLE_ENDPOINT`: Enter the endpoint URL for the DALL-E 3 deployment.
     - `AZURE_OPENAI_DALLE_DEPLOYMENT_NAME`: Enter the deployment name for DALL-E 3.

   Example configuration in the YAML file:
   ```yaml
   - name: AZURE_OPENAI_API_VERSION
     value: "2024-07-01-preview"
   - name: AZURE_OPENAI_DEPLOYMENT_NAME
     value: "gpt-4-deployment"
   - name: AZURE_OPENAI_ENDPOINT
     value: "https://<your-openai-resource-name>.openai.azure.com/"
   - name: AZURE_OPENAI_DALLE_ENDPOINT
     value: "https://<your-openai-resource-name>.openai.azure.com/"
   - name: AZURE_OPENAI_DALLE_DEPLOYMENT_NAME
     value: "dalle-3-deployment"
   ```

## Step 4: Deploy the ConfigMaps and Secrets
- Deploy the ConfigMap for RabbitMQ Plugins:
   ```bash
   kubectl apply -f config-maps.yaml
   ```
- Create and Deploy the Secret for OpenAI API:  
   - Make sure that you have replaced Base64-encoded-API-KEY in secrets.yaml with your Base64-encoded OpenAI API key.
   ```bash
   kubectl apply -f secrets.yaml
   ```
- Verify:
   ```bash
   kubectl get configmaps
   kubectl get secrets
   ```

## Step 5: Deploy the Application
   ```bash
   kubectl apply -f aps-all-in-one.yaml
   ```
### Validate the Deployment
- Check Pods and Services:
   ```bash
   kubectl get pods
   kubectl get services
   ```
- Test Frontend Access:
   - Locate the external IPs for store-front and store-admin services:
   ```bash
   kubectl get services
   ```
   - Access the Store Front app at the external IP on port 80.
   - Access the Store Admin app at the external IP on port 80.
## Step 6: Deploy Virtual Customer and Worker
   ```bash
   kubectl apply -f admin-tasks.yaml
   ```
- Monitor Virtual Customer:
   ```bash
   kubectl logs -f deployment/virtual-customer
   ```
- Monitor Virtual Worker:
   ```bash
   kubectl logs -f deployment/virtual-worker
   ```

## Step 7: Scale and Monitor Services
### Scale Deployments:
- Scale the `order-service` to 3 replicas:
```bash
kubectl scale deployment order-service --replicas=3
```
- Check Scaling:
```bash
kubectl get pods
```
- Monitor Resource Usage:

   - Enable metrics server for resource monitoring.
   - Use kubectl top to monitor pod and node usage:
   ```bash
   kubectl top pods
   kubectl top nodes
   ```

## Step 8: Explore Advanced Features
### AI-Generated Descriptions and Images:
- Use the AI Service for generating product descriptions and images.
- Ensure your OpenAI API key is correctly configured in the deployed secret.
### RabbitMQ Management:
- Access the RabbitMQ management UI:
   ```bash
   kubectl port-forward service/rabbitmq 15672:15672
   ```
   The kubectl port-forward command is used to forward a local port to a port on a Kubernetes resource (e.g., a Pod or Service). This allows you to access the application running in the cluster from your local machine without exposing it externally.


- Login with the default credentials (`username`/`password`).

### MongoDB Shell Access and Database Exploration
In this section, you will use the MongoDB shell to interact with the `orderdb` database, which stores order information for the Algonquin Pet Store application. Follow the steps below to connect to the MongoDB pod and explore its contents.

#### **1- Access the MongoDB Shell**
Run the following command to connect to the MongoDB shell inside the running MongoDB pod:
```bash
kubectl exec -it <mongodb-pod-name> -- mongo
```
Explanation: This command uses kubectl exec to open an interactive shell (-it) inside the MongoDB pod and starts the MongoDB shell program (mongo).

#### **2- List All Databases**
Once inside the MongoDB shell, run:
```bash
show dbs
```
Explanation: The show dbs command lists all databases available on the MongoDB server. You should see a list that includes the orderdb, which stores order-related data for the application.
#### **3- Switch to the Order Database**
```bash
use orderdb
```
Explanation: The use orderdb command selects the orderdb database, making it the active database for subsequent queries and commands.
#### **4- List Collections in the Database**
Display all collections in the orderdb database:
```bash
show collections
```
Explanation: The show collections command lists all collections (similar to tables in relational databases) in the current database. The orders collection contains the order data.
#### **5- Query the Orders Collection**
Retrieve all documents in the orders collection:
```bash
db.orders.find()
```
Explanation: The db.orders.find() command fetches and displays all documents (records) in the orders collection. This allows you to view the stored order data, including details such as customer information, products, and order status.

#### By following these steps, you will:
- Connect to the MongoDB shell in the Kubernetes pod.
- Explore the databases and collections used by the application.
- Query the orders collection to examine the data structure and stored records.


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

