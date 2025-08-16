# Deployment Guide

This guide provides step-by-step instructions for deploying the RuyaTracker CLM Enterprise application to Vercel with CI/CD pipeline setup.

## 📋 Prerequisites

Before starting the deployment process, ensure you have:

- [x] GitHub account
- [x] Vercel account (free tier available)
- [x] Node.js 18+ installed locally
- [x] Git installed locally
- [x] Basic knowledge of Git and GitHub

## 🚀 Deployment Methods

### Method 1: One-Click Deployment (Recommended for Quick Start)

1. **Click the Deploy Button**
   
   [![Deploy to Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/your-username/ruyatrack-clm-enterprise)

2. **Configure Repository**
   - Choose a repository name
   - Select your GitHub account
   - Click "Create"

3. **Environment Configuration**
   - Add environment variables (optional for basic deployment)
   - Click "Deploy"

4. **Access Your Application**
   - Wait for deployment to complete
   - Click "Visit" to access your live application

### Method 2: Manual Setup with Full CI/CD (Recommended for Production)

#### Step 1: Repository Setup

1. **Fork the Repository**
   ```bash
   # Go to GitHub and fork the repository
   # Then clone your fork
   git clone https://github.com/YOUR_USERNAME/ruyatrack-clm-enterprise.git
   cd ruyatrack-clm-enterprise
   ```

2. **Install Dependencies**
   ```bash
   npm install
   ```

3. **Test Locally**
   ```bash
   npm run dev
   # Visit http://localhost:3000 to verify everything works
   ```

#### Step 2: Vercel Project Setup

1. **Install Vercel CLI**
   ```bash
   npm install -g vercel
   ```

2. **Login to Vercel**
   ```bash
   vercel login
   ```

3. **Link Project**
   ```bash
   vercel link
   # Follow prompts to create new project or link existing
   ```

4. **Get Project Information**
   ```bash
   # Get your Vercel project ID and org ID
   cat .vercel/project.json
   ```

#### Step 3: GitHub Secrets Configuration

1. **Go to Repository Settings**
   - Navigate to your GitHub repository
   - Go to `Settings > Secrets and variables > Actions`

2. **Add Required Secrets**
   ```
   VERCEL_TOKEN=your_vercel_token
   VERCEL_ORG_ID=your_vercel_org_id
   VERCEL_PROJECT_ID=your_vercel_project_id
   ```

3. **Get Vercel Token**
   - Go to [Vercel Account Settings](https://vercel.com/account/tokens)
   - Create new token with appropriate scope
   - Copy token value

4. **Get Org and Project IDs**
   ```bash
   # From .vercel/project.json after running vercel link
   {
     "projectId": "prj_xxxxxxxxxxxxxxxxxxxx",
     "orgId": "team_xxxxxxxxxxxxxxxxxxxx"
   }
   ```

#### Step 4: Environment Configuration

1. **Create Environment Files**
   ```bash
   cp .env.example .env.local
   ```

2. **Configure Variables**
   ```env
   # .env.local
   NEXT_PUBLIC_APP_NAME=RuyaTracker CLM Enterprise
   NEXT_PUBLIC_APP_VERSION=1.0.0
   NEXT_PUBLIC_APP_ENV=production
   ```

3. **Add to Vercel Dashboard**
   - Go to Vercel project dashboard
   - Navigate to `Settings > Environment Variables`
   - Add production environment variables

#### Step 5: Deploy and Test

1. **Push to Trigger Deployment**
   ```bash
   git add .
   git commit -m "Initial deployment setup"
   git push origin main
   ```

2. **Monitor Deployment**
   - Check GitHub Actions tab for pipeline status
   - Monitor Vercel dashboard for deployment progress

3. **Verify Deployment**
   - Visit production URL
   - Test all functionality
   - Check performance metrics

## 🔧 Advanced Configuration

### Custom Domain Setup

1. **Add Domain in Vercel**
   - Go to project settings
   - Navigate to "Domains"
   - Add your custom domain

2. **Configure DNS**
   ```
   Type: CNAME
   Name: www (or @)
   Value: cname.vercel-dns.com
   ```

3. **SSL Certificate**
   - Vercel automatically provisions SSL certificates
   - Verify HTTPS is working

### Environment-Specific Deployments

#### Production Environment
```bash
# Triggered by push to main branch
git checkout main
git merge develop
git push origin main
```

#### Staging Environment
```bash
# Triggered by push to develop branch
git checkout develop
git push origin develop
```

#### Preview Deployments
```bash
# Triggered by pull requests
git checkout -b feature/new-feature
git push origin feature/new-feature
# Create pull request
```

### Performance Optimization

1. **Enable Vercel Analytics**
   ```json
   // vercel.json
   {
     "analytics": {
       "enable": true
     }
   }
   ```

2. **Configure Caching**
   ```json
   // vercel.json
   {
     "headers": [
       {
         "source": "/static/(.*)",
         "headers": [
           {
             "key": "Cache-Control",
             "value": "public, max-age=31536000, immutable"
           }
         ]
       }
     ]
   }
   ```

3. **Optimize Images**
   - Use WebP format when possible
   - Implement lazy loading
   - Compress images before deployment

## 🔒 Security Configuration

### Security Headers

The application includes comprehensive security headers:

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "X-Frame-Options",
          "value": "DENY"
        },
        {
          "key": "X-XSS-Protection",
          "value": "1; mode=block"
        },
        {
          "key": "Referrer-Policy",
          "value": "strict-origin-when-cross-origin"
        },
        {
          "key": "Content-Security-Policy",
          "value": "default-src 'self' 'unsafe-inline' 'unsafe-eval' data: blob:;"
        }
      ]
    }
  ]
}
```

### Environment Security

1. **Secure Secrets Management**
   - Never commit secrets to repository
   - Use GitHub Secrets for CI/CD
   - Use Vercel Environment Variables for runtime

2. **Access Control**
   - Limit repository access
   - Use branch protection rules
   - Require pull request reviews

## 📊 Monitoring and Maintenance

### Performance Monitoring

1. **Vercel Analytics**
   - Real-time performance metrics
   - Core Web Vitals tracking
   - User experience insights

2. **Lighthouse CI**
   - Automated performance audits
   - Accessibility checks
   - SEO optimization

### Health Checks

1. **Uptime Monitoring**
   ```bash
   # Add to your monitoring service
   curl -f https://your-domain.com/health || exit 1
   ```

2. **Performance Monitoring**
   - Set up alerts for performance degradation
   - Monitor Core Web Vitals
   - Track error rates

### Backup and Recovery

1. **Code Backup**
   - Repository is automatically backed up on GitHub
   - Consider additional backup strategies for critical deployments

2. **Configuration Backup**
   - Export Vercel project settings
   - Document environment variables
   - Maintain deployment runbooks

## 🚨 Troubleshooting

### Common Issues

1. **Build Failures**
   ```bash
   # Check build logs
   vercel logs
   
   # Test build locally
   npm run build
   ```

2. **Environment Variable Issues**
   ```bash
   # Verify environment variables
   vercel env ls
   
   # Pull environment variables
   vercel env pull .env.local
   ```

3. **Domain Configuration Issues**
   ```bash
   # Check DNS configuration
   nslookup your-domain.com
   
   # Verify SSL certificate
   openssl s_client -connect your-domain.com:443
   ```

### Getting Help

1. **Vercel Support**
   - [Vercel Documentation](https://vercel.com/docs)
   - [Vercel Community](https://github.com/vercel/vercel/discussions)

2. **GitHub Actions**
   - [GitHub Actions Documentation](https://docs.github.com/en/actions)
   - Check workflow logs in Actions tab

3. **Project Support**
   - Create issue in repository
   - Check existing documentation
   - Contact support team

## 📈 Scaling Considerations

### Traffic Scaling

1. **Vercel Limits**
   - Free tier: 100GB bandwidth/month
   - Pro tier: 1TB bandwidth/month
   - Enterprise: Custom limits

2. **Performance Optimization**
   - Enable edge caching
   - Optimize bundle size
   - Use CDN for static assets

### Feature Scaling

1. **Backend Integration**
   - Plan for API integration
   - Consider serverless functions
   - Implement proper error handling

2. **Database Integration**
   - Plan data persistence strategy
   - Consider database hosting options
   - Implement backup strategies

---

**Next Steps:**
- [Configuration Guide](CONFIGURATION.md)
- [Troubleshooting Guide](TROUBLESHOOTING.md)
- [API Integration Guide](API_INTEGRATION.md)

