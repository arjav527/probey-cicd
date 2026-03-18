# Docker CI/CD Pipeline Setup Guide

## 📋 Prerequisites

1. **Docker Hub Account** - Create at https://hub.docker.com
2. **GitHub Repository** - Push your code to GitHub

## 🔐 GitHub Secrets Configuration

Go to your GitHub repository → Settings → Secrets and variables → Actions

Add these repository secrets:

- `DOCKER_USERNAME` - Your Docker Hub username
- `DOCKER_PASSWORD` - Your Docker Hub password or access token

## 🚀 Quick Start

### 1. Update docker-compose.yml
Replace `your-dockerhub-username` with your actual Docker Hub username in `docker-compose.yml`

### 2. Customize for Your Application

**For Node.js apps:**
- Keep the current Dockerfile (already configured for Node.js)
- Ensure you have a `server.js` or update the CMD in Dockerfile

**For Python apps:**
Update Dockerfile:
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8080
CMD ["python", "app.py"]
```

**For Java/Spring Boot:**
Update Dockerfile:
```dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","app.jar"]
```

### 3. Push to GitHub

```bash
git init
git add .
git commit -m "Add Docker CI/CD pipeline"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

## 🎯 Pipeline Workflow

1. **On every push/PR:**
   - Builds Docker image
   - Runs container tests
   - Saves image as artifact

2. **Security scan:**
   - Scans Docker image for vulnerabilities using Trivy
   - Blocks on CRITICAL/HIGH severity issues

3. **Deploy to Staging (main branch only):**
   - Pushes image to Docker Hub with `staging` tag
   - Deploys using docker-compose
   - Verifies deployment

4. **Deploy to Production (after staging succeeds):**
   - Pushes image with `latest` and `production` tags
   - Redeploys with force-recreate
   - Cleans up old images

## 🧪 Local Testing

Test your Docker setup locally before pushing:

```bash
# Build Docker image
docker build -t your-username/ci-cd:test .

# Run container
docker run -p 8080:8080 your-username/ci-cd:test

# Test with docker-compose
docker-compose up --build
```

## 📦 Environment Variables

Configure in docker-compose.yml or GitHub Actions:
- `NODE_ENV` - Application environment
- `PORT` - Application port (default: 8080)

## 🔧 Customization Options

### Add Database Support
Uncomment the database section in `docker-compose.yml`

### Change Registry
Update `REGISTRY` in `.github/workflows/ci-cd.yml`:
```yaml
env:
  REGISTRY: ghcr.io  # GitHub Container Registry
```

### Add Health Checks
Update the health check URL in Dockerfile and docker-compose.yml to match your application's health endpoint.

## 🐛 Troubleshooting

**Build fails:** Check Dockerfile syntax and ensure all required files exist

**Deployment fails:** Verify Docker credentials and network connectivity

**Tests fail:** Review container logs with `docker logs <container-id>`

## 📝 Additional Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Documentation](https://docs.docker.com/)
- [Trivy Scanner](https://aquasecurity.github.io/trivy/)
