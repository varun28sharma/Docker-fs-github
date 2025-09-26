# AWS EC2 Deployment Guide

This guide will help you deploy your e-commerce application to AWS EC2 using GitHub Actions.

## 🚀 Prerequisites

### 1. AWS EC2 Instance Setup

1. **Launch an EC2 instance**:
   - Choose Ubuntu 22.04 LTS (recommended)
   - Instance type: t3.medium or larger (for better performance)
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

3. **Connect to your EC2 instance**:
   ```bash
   ssh -i your-key.pem ubuntu@your-ec2-public-ip
   ```

### 2. GitHub Repository Secrets

Add these secrets to your GitHub repository:

1. Go to your repository → **Settings** → **Secrets and variables** → **Actions**
2. Add the following secrets:

   - **`EC2_HOST`**: Your EC2 public IP address (e.g., `54.123.45.67`)
   - **`EC2_SSH_KEY`**: Your private SSH key content (the entire content of your `.pem` file)

## 🔧 Setup Instructions

### Step 1: Prepare Your EC2 Instance

1. **Update the system**:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. **Install required packages**:
   ```bash
   sudo apt install -y curl wget git
   ```

3. **Create deployment directory**:
   ```bash
   mkdir -p ~/ecommercefullstack
   ```

### Step 2: Configure GitHub Secrets

1. **Get your EC2 public IP**:
   - Go to AWS Console → EC2 → Instances
   - Copy the public IPv4 address

2. **Get your SSH private key**:
   - Download your `.pem` file from AWS
   - Copy the entire content (including `-----BEGIN RSA PRIVATE KEY-----` and `-----END RSA PRIVATE KEY-----`)

3. **Add secrets to GitHub**:
   - Go to your repository → Settings → Secrets and variables → Actions
   - Click "New repository secret"
   - Add `EC2_HOST` with your EC2 public IP
   - Add `EC2_SSH_KEY` with your private key content

### Step 3: Deploy Using GitHub Actions

1. **Push your code**:
   ```bash
   git add .
   git commit -m "Add EC2 deployment workflow"
   git push origin main
   ```

2. **Monitor the deployment**:
   - Go to your repository → Actions tab
   - Watch the "Deploy Ecommerce Fullstack App on EC2" workflow
   - Check the logs for any errors

### Step 4: Access Your Application

After successful deployment, your application will be available at:

- **Frontend**: `http://your-ec2-public-ip`
- **Backend API**: `http://your-ec2-public-ip:8081`
- **Nginx (Alternative)**: `http://your-ec2-public-ip:8080`

## 🐳 Docker Commands for Manual Deployment

If you need to deploy manually on EC2:

```bash
# SSH into your EC2 instance
ssh -i your-key.pem ubuntu@your-ec2-public-ip

# Navigate to your project directory
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
   - Check your security group allows SSH (port 22)
   - Verify your SSH key is correct
   - Ensure your EC2 instance is running

2. **Docker Installation Failed**:
   - Check if the instance has enough resources
   - Verify internet connectivity
   - Check the GitHub Actions logs

3. **Application Not Accessible**:
   - Check security group allows HTTP/HTTPS traffic
   - Verify containers are running: `sudo docker ps`
   - Check application logs: `sudo docker logs container-name`

4. **Database Connection Issues**:
   - Check if MySQL container is running
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
- [ ] Use AWS RDS instead of containerized database for production

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

### Useful AWS Resources

- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS Security Groups](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html)
- [Docker on AWS](https://aws.amazon.com/docker/)

---

**Happy Deploying! 🎉**

Your e-commerce application is now ready for AWS EC2 deployment with automated CI/CD!
