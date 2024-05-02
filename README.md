# Microservices E-commerce

- `git clone https://github.com/faizan35/e-Commerce_Argo_CD.git`

### Prerequisites

### For EKS

- AWS account

## Task 1: Setup and Configuration

### 1.1. Setting up EKS Cluster

- Follow the detailed guide to setup you EKS cluster. [here](./EKS-Setup/Steps-To-EKS.md)

### 1.2. Install Argo CD on Your Kubernetes Cluster:

- Best practice in production is to deploy Argo in a separate cluster with high availability.
- This ensures Argo remains unaffected by outages in other Kubernetes clusters, maintaining smooth continuous delivery operations.
- But this is a Demo Project anything will work.
- Documentation: [link](https://argo-cd.readthedocs.io/en/stable/getting_started/)

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

#### 1.2.1 To use the GUI

- Follow the steps form [here](https://github.com/faizan35/Argo_CD_Mastery_Hub/blob/main/Module-02/2.1-Installation-Methods.md).

- **For Minikube only**, port forwarding: `minikube service argocd-server -n argocd`
- **For EKS**:
  - Expose the `NodePort` of `argocd-server` service in your aws security group.
  - to access the dashboard use the ip address of the node-group and port `NordPort`.

### 1.3. Install Argo Rollouts:

```bash
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```

#### 1.3.1 To use the GUI

- Follow the steps form [here](https://github.com/faizan35/Argo_CD_Mastery_Hub/blob/main/Module-0x/x.1-Installation-Methods.md).

- **For EKS**:
  - For dashboard you hava to use the IP of the VM in which you are executing the commands.
  - Expose the `3100` Port in your security group, of the VM.
  - open a new terminal and execute this command to see the dashboard: `http://<IP>:3100/rollouts`

## Task 2: Creating the GitOps Pipeline

### 2.1. Dockerize the Application

> We will be creating a CI/CD Jenkins Pipeline, to Build and Upload the image to DockerHub.
> But for now, we will do this step manually.

##### Docker Image in DockerHub Registry

1. **Frontend Image:** `faizan44/e-com_micro_frontend:latest`

2. **Backend Image**: `faizan44/e-com_micro_backend:latest`

### 2.2. Deploy the Application Using Argo CD:

#### 2.2.1. For Minikube Setup only

- access the Frontend.: `minikube service frontend-service -n e-com`

- Port forwardding or tunnelling: `minikube service api -n e-com`

- Paste the `http://<IP>:<PORT>`, in the fronend deployment manifest.

#### 2.2.2. For EKS

##### 2.2.2.1. Paste the copied ingress endpoint to...

- frontend deployment, inside `k8s-plain/plain.yml` value of `REACT_APP_API_URL`.
- rollout manifest here, inside `Argo-Rollout/rollout.yml` value of `REACT_APP_API_URL`.

and commit the changes.

> **Note:** this is done if you dont have a domain.

##### 2.2.2.2. Creating Application with Argo CD

1. After login into the ArgoCD dashboard.

  <img src="./Img/argocd-dashboard.png">

1. Click on `+ NEW APP`
2. Click on `EDIT AS YAML`
3. Paste this yaml and click on `SAVE`

   ```yml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: e-commerce
   spec:
     destination:
       name: ""
       namespace: e-com
       server: "https://kubernetes.default.svc"
     source:
       path: k8s-plain
       repoURL: "https://github.com/faizan35/e-Commerce_Argo_CD"
       targetRevision: HEAD
     sources: []
     project: default
     syncPolicy:
       automated:
         prune: false
         selfHeal: false
   ```

4. Click on `CREATE`

- Your Applications should start creating.

<img src="./Img/start-creating.png">

<img src="./Img/app-tree.png">

#### 2.2.2.3. Argo CD to monitor the Repository

##### a. With Jenkins CI/CD Pipeline

- We will trigger ArgoCD by jenkins POST build pipeline. It will trigger the webhook associated with ArgoCD.

##### b. Without Jenkins

- We will set the webhook but we will manually make commit to the git repo.

##### Setting the Webhook

- **Payload**: `https://34.220.39.20:30828/api/webhook`
- **Payload**: `application/json`

- Click on `Add Webhook`

- <img src="./Img/webhook.png">

##### Testing Webhook

- frontend deployment, inside `k8s-plain/plain.yml` make `spec.replicas` count to 3, and commit changes.
- See the below video.

<video width="320" height="240" controls>
  <source src="./Img/webhook-testing.webm" type="video/mp4">
</video>

## Task 3: Implementing a Canary Release with Argo Rollouts

- kubectl apply -f https://raw.githubusercontent.com/faizan35/e-Commerce_Argo_CD/main/Argo-Rollout/rollout.yml -n e-com

- kubectl apply -f https://raw.githubusercontent.com/faizan35/e-Commerce_Argo_CD/main/Argo-Rollout/service.yml -n e-com

- kubectl argo rollouts get rollout frontend-rollout -n e-com --watch

- deelte rollout: `kubectl delete rollout frontend-rollout -n e-com`

## Task 4: Documentation and Cleanup

### Cleanup

- To delete the EKS cluster.

```bash
eksctl delete cluster --name e-com-cluster --region us-west-2
```

---

This project is designed as an example of an e-commerce application using **microservices architecture**. It consists of separate components for the backend, frontend, and a MongoDB database for storing product information.

<img src="./Resource/frontpage.png">

## Setting EKS Cluster on AWS - [here](./EKS-Setup/Steps-To-EKS.md)

###### Video Comming Soon...

## Jenkins Server setup with Terraform - [here](./Jenkins-Steup-Terraform-Ansible/Setting-Jenkins-server.md)

###### Video Comming Soon...

## CI/CD Pipeline for Deployment/Rollback - [here](./cicd-pipeline-jenkins/cicd-pipeline.md)

###### Video Comming Soon...

---

## Getting Started for Develpment

**To run the project locally, follow these steps:**

##### 1. Clone Repo

```bash
git clone https://github.com/faizan35/e-Commerce_Microservices_Communication.git
```

```bash
cd e-Commerce_Microservices_Communication/
```

##### 2. cd and install

```bash
bash ./scripts/npm_install.sh
```

##### 3. Create `.env` files

- Inside `/backend` dir.

  ```env
  MONGODB_URI=mongodb://127.0.0.1:27017/e-commerce
  # MONGODB_USER=admin
  # MONGODB_PASS=password123

  # USE_DB_AUTH=true

  PORT=8000
  HOST=0.0.0.0
  ```

- Inside `/frontend` dir.
  ```env
  REACT_APP_API_URL=http://localhost:8000
  ```

##### 4. Start (frontend, backend and database)

Open teminals, execute both command in both of them.

1. Run MongoDB Server.
2. Execute frontend first.

   ```bash
   cd frontend
   npm start
   ```

3. Execute backend then.

   ```bash
   node backend/backend.js
   ```

##### 5. Access the frontend in your browser at http://localhost:3000.

## Docker

### Build Docker Images

1. Frontend

```bash
cd frontend/
sudo docker build -t faizan44/e-com_micro_frontend:latest .
```

2. Backend

```bash
cd backend/
sudo docker build -t faizan44/e-com_micro_backend:latest .
```

### Push to DockerHub

- `sudo docker push faizan44/e-com_micro_frontend`
- `sudo docker push faizan44/e-com_micro_backend`

### Run with Docker-Compose

1. Install Docker-Compose

```bash
sudo apt  install docker-compose
```

2. Run

```bash
docker-compose up -d
```

3. down

```bash
docker-compose down
```

### Pull from DockerHUB and Run

1. Pull both images.

   ```bash
   sudo docker pull faizan44/e-com_micro_frontend
   sudo docker pull faizan44/e-com_micro_backend
   ```

2. Create `docker-compose.yml` file

- Just replace this,
  ```yaml
  build:
  context: ./frontend
  ```
- With

  ```yaml
  image: faizan44/e-com_micro_frontend
  ```

- Do same for **backend**, and run.

3. Run this command where your `docker-compose.yml` file is located.

```bash
docker-compose up -d
```

## API Endpoints

- **GET /api/products** Retrieve the list of products.
- **POST /api/products** Add a new product.
- **PUT /api/products/:id** Update an existing product.
- **DELETE /api/products/:id** Delete a product.

## Frontend Usage

- Click the "**Get Products**" button to fetch and display the list of products.
- Add, update, or delete products using the respective sections on the webpage.

## Database

The project uses MongoDB to store product data. Ensure you have MongoDB installed and running locally.

## Technologies Used

- Node.js
- Express.js
- CORS
- MongoDB
- Mongoose
- HTML, CSS, JS

## Contributing

Feel free to contribute to the project by submitting issues or pull requests.

## License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

---
