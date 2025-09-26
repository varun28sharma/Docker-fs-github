# E-commerce Fullstack App - EC2 Deployment Guide

This guide will help you deploy your e-commerce application to AWS EC2 using GitHub Actions and Docker.

## 🚀 Quick Start

### Prerequisites
1. **AWS EC2 Instance** (Ubuntu 22.04 LTS recommended)
2. **GitHub Repository** with your code
3. **SSH Key Pair** for EC2 access

### Step 1: Set up AWS EC2 Instance

1. **Launch EC2 Instance**:
   - AMI: Ubuntu Server 22.04 LTS
   - Instance Type: t3.medium or larger
   - Storage: 20GB minimum
   - Security Group: Allow inbound traffic on ports 22, 80, 443, 8081

2. **Security Group Configuration**:
   ```
   Type           Protocol    Port Range    Source
   SSH            TCP         22            Your IP
   HTTP           TCP         80            0.0.0.0/0
   HTTPS          TCP         443           0.0.0.0/0
   Custom TCP     TCP         8081          0.0.0.0/0
   ```

### Step 2: Configure GitHub Secrets

1. Go to your repository → **Settings** → **Secrets and variables** → **Actions**
2. Add these secrets:

   - **`EC2_HOST`**: Your EC2 public IP address (e.g., `54.123.45.67`)
   - **`EC2_SSH_KEY`**: Your private SSH key content (entire `.pem` file content)

### Step 3: Deploy

1. **Push your code**:
   ```bash
   git add .
   git commit -m "Add EC2 deployment workflow"
   git push origin main
   ```

2. **Monitor deployment**:
   - Go to your repository → Actions tab
   - Watch the "Deploy Ecommerce Fullstack App on EC2" workflow

### Step 4: Access Your Application

After successful deployment:
- **Frontend**: `http://your-ec2-public-ip`
- **Backend API**: `http://your-ec2-public-ip:8081`
- **Nginx (Alternative)**: `http://your-ec2-public-ip:8080`

## 📁 Project Structure

```
DockerFSGIT/
├── .github/workflows/
│   └── deploy.yml                 # GitHub Actions deployment workflow
├── docker_files/
│   ├── Dockerfile.backend         # Backend Docker configuration
│   ├── Dockerfile.frontend        # Frontend Docker configuration
│   ├── docker-compose.yml         # Local development compose
│   └── nginx.conf                 # Nginx configuration
├── docker-ecommerce-backend/      # Spring Boot backend
├── docker-ecommerce-frontend/     # React frontend
├── docker-compose.ec2.yml         # Production EC2 compose
├── env.ec2                        # Environment variables template
└── DEPLOYMENT-GUIDE.md           # This guide
```

## 🐳 Docker Configuration

### Backend Dockerfile
- **Base Image**: Maven 3.9.6 with Eclipse Temurin 21
- **Build Process**: Multi-stage build for optimized image size
- **Port**: 8081

### Frontend Dockerfile
- **Base Image**: Node.js 20 Alpine
- **Build Process**: Vite build with Nginx serving
- **Port**: 80

### Database
- **Image**: MySQL 8.0
- **Port**: 3306
- **Data Persistence**: Docker volume

## 🔧 Environment Variables

Edit `env.ec2` file with your configuration:

```bash
# Database Configuration
DB_ROOT_PASSWORD=your_secure_password
DB_USER=ecommerce_user
DB_PASSWORD=your_db_password
DB_PORT=3306

# Application Configuration
BACKEND_PORT=8081
FRONTEND_PORT=80

# Security
JWT_SECRET=your-super-secret-jwt-key

# Production URLs
FRONTEND_URL=http://your-ec2-public-ip
BACKEND_URL=http://your-ec2-public-ip:8081
```

## 🚀 Deployment Process

### What the GitHub Actions Workflow Does:

1. **Checkout Repository**: Downloads your code
2. **List Files**: Debug step to verify file structure
3. **Build Docker Images**: Tests Docker build locally
4. **Copy to EC2**: Transfers files to your EC2 instance
5. **Install Dependencies**: Installs Git and Docker on EC2
6. **Deploy**: Runs `docker compose up -d --build`
7. **Verify**: Shows running containers and access URLs

### Manual Deployment Commands

If you need to deploy manually on EC2:

```bash
# SSH into your EC2 instance
ssh -i your-key.pem ubuntu@your-ec2-public-ip

# Navigate to project directory
cd ~/ecommercefullstack

# Copy environment file
cp env.ec2 .env

# Edit environment variables
nano .env

# Deploy with Docker Compose
sudo docker compose -f docker-compose.ec2.yml up -d --build

# Check running containers
sudo docker ps

# View logs
sudo docker compose -f docker-compose.ec2.yml logs -f
```

## 🔍 Troubleshooting

### Common Issues

1. **SSH Connection Failed**:
   - Check security group allows SSH (port 22)
   - Verify SSH key is correct
   - Ensure EC2 instance is running

2. **Docker Build Failed**:
   - Check Dockerfile syntax
   - Verify file paths in COPY commands
   - Check GitHub Actions logs

3. **Application Not Accessible**:
   - Check security group allows HTTP/HTTPS traffic
   - Verify containers are running: `sudo docker ps`
   - Check application logs: `sudo docker logs container-name`

4. **Database Connection Issues**:
   - Check MySQL container is running
   - Verify environment variables
   - Check database logs: `sudo docker logs ecommerce-db`

### Debug Commands

```bash
# Check system resources
htop
df -h
free -h

# Check Docker status
sudo systemctl status docker
sudo docker ps -a
sudo docker images

# Check application logs
sudo docker compose -f docker-compose.ec2.yml logs backend
sudo docker compose -f docker-compose.ec2.yml logs frontend
sudo docker compose -f docker-compose.ec2.yml logs db

# Check network connectivity
curl -I http://localhost:8081
curl -I http://localhost:80
```

## 🔒 Security Considerations

### Production Security Checklist

- [ ] Change default passwords in `env.ec2`
- [ ] Use strong JWT secrets
- [ ] Set up SSL/TLS certificates
- [ ] Configure firewall rules
- [ ] Regular security updates
- [ ] Database encryption
- [ ] Use AWS RDS for production database

### SSL/HTTPS Setup

1. **Get SSL certificate** (Let's Encrypt):
   ```bash
   sudo apt install certbot
   sudo certbot certonly --standalone -d your-domain.com
   ```

2. **Update nginx configuration** for HTTPS
3. **Update security group** to allow HTTPS traffic

## 📊 Monitoring and Maintenance

### Health Checks

The application includes health checks for all services:
- Database: MySQL ping
- Backend: Spring Boot actuator health endpoint
- Frontend: HTTP response check

### Log Management

```bash
# View real-time logs
sudo docker compose -f docker-compose.ec2.yml logs -f

# View specific service logs
sudo docker compose -f docker-compose.ec2.yml logs -f backend
sudo docker compose -f docker-compose.ec2.yml logs -f frontend

# Save logs to file
sudo docker compose -f docker-compose.ec2.yml logs > deployment.log
```

### Backup

```bash
# Backup database
sudo docker exec ecommerce-db mysqldump -u root -p ecommerce > backup.sql

# Backup application files
tar -czf app-backup.tar.gz ~/ecommercefullstack
```

## 🚀 Scaling

### Horizontal Scaling

1. **Load Balancer**: Use AWS Application Load Balancer
2. **Multiple Instances**: Deploy to multiple EC2 instances
3. **Auto Scaling**: Use AWS Auto Scaling Groups
4. **Database**: Use AWS RDS for managed database

### Vertical Scaling

1. **Instance Type**: Upgrade to larger instance types
2. **Storage**: Increase EBS volume size
3. **Memory**: Add more RAM for better performance

## 💰 Cost Optimization

### Tips to Reduce Costs

1. **Use Spot Instances** for development
2. **Stop instances** when not in use
3. **Use smaller instance types** for development
4. **Monitor usage** with AWS Cost Explorer
5. **Use AWS Free Tier** for testing

## 📞 Support

### Getting Help

1. Check GitHub Actions logs
2. Review EC2 instance logs
3. Check Docker container logs
4. Verify security group settings
5. Check AWS CloudWatch logs

### Useful Resources

- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [Docker Documentation](https://docs.docker.com/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

---

**Happy Deploying! 🎉**

Your e-commerce application is now ready for automated AWS EC2 deployment!
