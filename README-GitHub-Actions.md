# GitHub Actions Docker Deployment Guide

This guide will help you deploy your e-commerce application using GitHub Actions and Docker images.

## 🚀 Quick Start

### 1. Push to GitHub
```bash
git add .
git commit -m "Add GitHub Actions workflow"
git push origin main
```

### 2. GitHub Actions will automatically:
- ✅ Build and test your backend (Maven)
- ✅ Build and test your frontend (Node.js)
- ✅ Create Docker images for both services
- ✅ Push images to GitHub Container Registry (GHCR)
- ✅ Deploy to your chosen hosting service

## 📋 What's Included

### GitHub Actions Workflows

1. **`.github/workflows/docker-build.yml`**
   - Builds and tests both backend and frontend
   - Creates Docker images
   - Pushes to GitHub Container Registry
   - Runs on every push and pull request

2. **`.github/workflows/deploy-railway.yml`**
   - Deploys to Railway (if configured)
   - Runs after successful build

3. **`.github/workflows/deploy-render.yml`**
   - Deploys to Render (if configured)
   - Runs after successful build

### Docker Configuration

- **`docker_files/docker-compose.deploy.yml`**: Production-ready compose file
- **`docker_files/env.example`**: Environment variables template

## 🔧 Setup Instructions

### Step 1: Repository Setup

1. **Push your code to GitHub**
2. **Enable GitHub Container Registry**:
   - Go to your repository settings
   - Navigate to "Actions" → "General"
   - Under "Workflow permissions", select "Read and write permissions"
   - Check "Allow GitHub Actions to create and approve pull requests"

### Step 2: Choose Your Hosting Service

#### Option A: Railway (Recommended - Easy Setup)

1. **Sign up at [Railway](https://railway.app/)**
2. **Connect your GitHub account**
3. **Create a new project**:
   - Click "New Project"
   - Select "Deploy from GitHub repo"
   - Choose your repository
4. **Add environment variables**:
   ```
   DB_ROOT_PASSWORD=your_secure_password
   DB_USER=ecommerce_user
   DB_PASSWORD=your_db_password
   JWT_SECRET=your_jwt_secret_key
   ```
5. **Deploy using Docker**:
   - Railway will automatically detect your Docker setup
   - Use the images from GitHub Container Registry

#### Option B: Render

1. **Sign up at [Render](https://render.com/)**
2. **Create a new Web Service**
3. **Connect your GitHub repository**
4. **Configure deployment**:
   - Build Command: `docker-compose -f docker_files/docker-compose.deploy.yml up -d --build`
   - Start Command: `docker-compose -f docker_files/docker-compose.deploy.yml up`
5. **Add environment variables** (same as Railway)

#### Option C: DigitalOcean App Platform

1. **Sign up at [DigitalOcean](https://www.digitalocean.com/)**
2. **Create a new App**
3. **Connect your GitHub repository**
4. **Configure components**:
   - Backend service (using your backend Docker image)
   - Frontend service (using your frontend Docker image)
   - Database service (MySQL)

### Step 3: Configure Secrets (Optional)

If you want to use the deployment workflows, add these secrets to your GitHub repository:

**For Railway:**
- `RAILWAY_TOKEN`: Your Railway API token

**For Render:**
- `RENDER_SERVICE_ID`: Your Render service ID
- `RENDER_API_KEY`: Your Render API key

## 🐳 Docker Images

After pushing to GitHub, your images will be available at:

- **Backend**: `ghcr.io/your-username/your-repo-backend:latest`
- **Frontend**: `ghcr.io/your-username/your-repo-frontend:latest`

## 📊 Monitoring

### GitHub Actions Dashboard
- Go to your repository → "Actions" tab
- View build logs and deployment status
- Check for any errors or warnings

### Container Registry
- Go to your repository → "Packages" tab
- View your Docker images
- Check image sizes and tags

## 🔍 Troubleshooting

### Common Issues

1. **Build fails on Maven/Node.js**:
   - Check your `pom.xml` and `package.json` files
   - Ensure all dependencies are correct

2. **Docker build fails**:
   - Check Dockerfile syntax
   - Verify file paths in COPY commands

3. **Deployment fails**:
   - Check environment variables
   - Verify hosting service configuration
   - Check logs in the hosting service dashboard

### Debug Steps

1. **Check GitHub Actions logs**:
   - Go to Actions tab → Click on failed workflow
   - Review the logs for specific error messages

2. **Test locally**:
   ```bash
   # Test Docker build locally
   docker build -f docker_files/Dockerfile.backend -t test-backend .
   docker build -f docker_files/Dockerfile.frontend -t test-frontend .
   ```

3. **Check environment variables**:
   - Ensure all required variables are set
   - Use the `env.example` file as a template

## 🚀 Deployment Commands

### Manual Deployment (if needed)

```bash
# Pull latest images
docker pull ghcr.io/your-username/your-repo-backend:latest
docker pull ghcr.io/your-username/your-repo-frontend:latest

# Deploy using docker-compose
docker-compose -f docker_files/docker-compose.deploy.yml up -d
```

### Update Environment Variables

```bash
# Copy template
cp docker_files/env.example docker_files/.env

# Edit with your values
nano docker_files/.env

# Deploy with new environment
docker-compose -f docker_files/docker-compose.deploy.yml up -d
```

## 📈 Scaling

### Horizontal Scaling
- Use load balancers (nginx, cloud load balancers)
- Deploy multiple backend instances
- Use managed databases (AWS RDS, Google Cloud SQL)

### Vertical Scaling
- Increase container resources
- Optimize JVM settings for backend
- Use CDN for static assets

## 🔒 Security

### Production Checklist
- [ ] Change default passwords
- [ ] Use strong JWT secrets
- [ ] Enable HTTPS
- [ ] Set up proper firewall rules
- [ ] Regular security updates
- [ ] Database encryption

## 📞 Support

### Getting Help
1. Check GitHub Actions logs
2. Review hosting service documentation
3. Check Docker logs: `docker logs <container-name>`
4. Create an issue in your repository

### Useful Links
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Documentation](https://docs.docker.com/)
- [Railway Documentation](https://docs.railway.app/)
- [Render Documentation](https://render.com/docs)

---

**Happy Deploying! 🎉**

Your e-commerce application is now ready for automated deployment with GitHub Actions and Docker!
