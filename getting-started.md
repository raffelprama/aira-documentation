# Getting Started

This guide will help you set up **AIRA** on your local machine and Kubernetes environment.

---

## 📦 Prerequisites

Before installing, ensure you have the following:

- **Docker** (>= 20.x)
- **Kubernetes** (>= 1.24)
- **kubectl** (configured to access your cluster)
- **Helm** (>= 3.x)
- **Python** (>= 3.10) with `venv` or `conda`
- **GitHub Personal Access Token** (for accessing private repositories if needed)

---

## ⚙️ Installation

### 1. Clone the Repository
```bash
git clone https://github.com/your-org/aira.git
cd aira
```

### 2. Set Up Environment Variables

Copy the .env.example file and adjust the values:
```
cp .env.example .env
```
Update the following variables inside .env:
```
POSTGRES_PASSWORD=yourpassword
TOKEN_GITHUB=yourtoken
```
### 3. Build & Push Docker Images
```
docker build -t ghcr.io/your-org/aira:latest .
docker push ghcr.io/your-org/aira:latest
```
### 4. Deploy to Kubernetes
```
kubectl apply -f kubernetes/
```

---

## 🖥️ Local Development

To run locally with hot reload:
```
uvicorn app.main:app --reload
```
---
Visit: http://localhost:8000/docs

## ✅ Verification

Once deployed, check the logs:
```
kubectl logs -f deployment/aira
```



If successful, you should see:
```
🚀 AIRA is running and connected to Kubernetes
```

---
<div style="display: flex; justify-content: space-between;";align="center">
  <a href="index.md">⬅️ Previous</a>
  <a href="architecture/1_overview.md">Next ➡️</a>
</div>
