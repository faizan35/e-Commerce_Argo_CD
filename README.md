# Microservices E-commerce

## Task 1: Setup and Configuration

### 1.1. Install Argo CD on Your Kubernetes Cluster:

- Best practice in production is to deploy Argo in a separate cluster with high availability.
- This ensures Argo remains unaffected by outages in other Kubernetes clusters, maintaining smooth continuous delivery operations.
- But this is a Demo Project anything will work.
- Documentation: [link](https://argo-cd.readthedocs.io/en/stable/getting_started/)

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

#### 1.1.1 To use the GUI

- Follow the steps form [here]().

### 1.2. Install Argo Rollouts:

```bash
kubectl create namespace argo-rollouts
kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
```

#### 1.2.1 To use the GUI

- Follow the steps form [here]().

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
