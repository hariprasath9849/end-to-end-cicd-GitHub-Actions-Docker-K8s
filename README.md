# Project Documentation: End-to-End CI/CD (Node + Docker + K8s + GitHub Actions)

This project is a minimal Node.js (Express) app with a Docker build, Kubernetes manifests, and a GitHub Actions CI/CD pipeline that builds and pushes the image, then validates the deployment on a Kind cluster.

## 1) What is inside
- `app.js`: Express server returning a Hello World response.
- `package.json`: Node.js dependencies and scripts.
- `Dockerfile`: Container image build for the app.
- `k8s/`: Kubernetes Deployment and Service manifests.
- `.github/workflows/ci-cd.yaml`: CI/CD pipeline that builds, pushes, and tests on Kind.

## 2) Prerequisites
Local development:
- Node.js 20+
- npm

Docker workflow:
- Docker installed and running

Kubernetes workflow:
- kubectl
- (Optional) Kind for local cluster testing

CI/CD:
- GitHub repo with Actions enabled
- Docker Hub account

## 3) Run locally (Node only) - step by step
1. Install dependencies:
```bash
npm install
```
2. Start the server:
```bash
npm start
```
3. Open `http://localhost:3000/` to verify the response.

## 4) Build and run with Docker - step by step
1. Build the image:
```bash
docker build -t myapp:local .
```
2. Run the container:
```bash
docker run --rm -p 3000:3000 myapp:local
```
3. Open `http://localhost:3000/`.

## 5) Deploy to Kubernetes (local Kind) - step by step
1. Create a Kind cluster:
```bash
kind create cluster --name myapp
```
2. Build the image locally:
```bash
docker build -t myapp:local .
```
3. Load the image into Kind:
```bash
kind load docker-image myapp:local --name myapp
```
4. Update `k8s/deployment.yaml` to use `myapp:local`, then apply:
```bash
kubectl apply -f k8s
```
5. Wait for rollout:
```bash
kubectl rollout status deployment/myapp-deployment
```
6. Port-forward and test:
```bash
kubectl port-forward svc/myapp-service 3000:3000
```
Open `http://localhost:3000/`.

## 6) CI/CD pipeline (GitHub Actions)
Workflow file: `.github/workflows/ci-cd.yaml`

### Steps executed on push to `main`
1. Checkout the code.
2. Setup Node.js 20.
3. Install dependencies.
4. Run tests (`npm test`, currently placeholder).
5. Login to Docker Hub (uses secrets).
6. Build Docker image `hariprasath9849/myapp:latest`.
7. Push image to Docker Hub.
8. Spin up a Kind cluster.
9. Pull and load the image into Kind.
10. Apply Kubernetes manifests from `k8s/`.
11. Wait for rollout and run a curl test.

### Required GitHub secrets
Set these in GitHub repo settings:
- `DOCKER_USERNAME`
- `DOCKER_PASSWORD`

## 7) Common operations
Update app response:
- Edit `app.js`, then rebuild and redeploy.

Change container port:
- Update `app.js`, `Dockerfile`, and `k8s/service.yaml` to match.

Update image name/tag:
- Update `.github/workflows/ci-cd.yaml` (build/push) and `k8s/deployment.yaml` (image).

## 8) Troubleshooting
- Pipeline fails at Docker login: check secrets `DOCKER_USERNAME`/`DOCKER_PASSWORD`.
- Pod not running: `kubectl describe pod <pod>` and check image name.
- Service not reachable: ensure port `3000` and `port-forward` is active.

## 9) Project structure
```
.
|-- .github/
|   `-- workflows/
|       `-- ci-cd.yaml
|-- k8s/
|   |-- deployment.yaml
|   `-- service.yaml
|-- app.js
|-- Dockerfile
|-- package.json
`-- package-lock.json
```
