# Task Tracker

A simple 3-tier Kubernetes application with:
- Frontend UI
- Flask backend API
- MongoDB database

This project is kept intentionally simple so it looks like a clean demo/prototype and can be extended later without extra complexity.

## Project structure

- `backend/` - Flask API
- `frontend/` - Nginx served HTML page
- `database/` - MongoDB initialization script
- `backend-tier.yaml` - backend Deployment + Service
- `database-tier.yaml` - MongoDB PVC + Deployment + Service
- `frontend-tier.yaml` - frontend Deployment + Service + Ingress
- `.github/workflows/ci.yml` - GitHub Actions CI pipeline
- `argocd/application.yaml` - ArgoCD deployment for continuous delivery

## Local app run

### Backend

```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python app.py
```

### Frontend

```bash
cd frontend
npm install
npm run build
```

### Database

Use MongoDB container or local MongoDB service.

## GitHub Actions CI

Create these GitHub repository secrets:
- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN`

The workflow builds and pushes:
- `<your-dockerhub-username>/task-tracker-backend`
- `<your-dockerhub-username>/task-tracker-frontend`
- `<your-dockerhub-username>/task-tracker-db`

## ArgoCD for CD

This project includes a very simple ArgoCD application definition for continuous delivery.

### Install ArgoCD on Minikube

```bash
minikube start --driver=docker
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Apply the app in ArgoCD

Update the GitHub repo URL in `argocd/application.yaml` and then run:

```bash
kubectl apply -f argocd/application.yaml
```

Then open the ArgoCD UI:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Login with the initial admin password and sync the app.

## Minikube deployment

```bash
kubectl apply -f backend-tier.yaml
kubectl apply -f database-tier.yaml
kubectl apply -f frontend-tier.yaml
```

Then check:

```bash
kubectl get pods -A
kubectl get svc -A
kubectl get ingress
```

## Notes

- This keeps the project simple and understandable for a demo/prototype.
- CI is done with GitHub Actions.
- CD is done with ArgoCD.
- This is the end-to-end flow: code push -> build image -> push to Docker Hub -> ArgoCD syncs deployment.
