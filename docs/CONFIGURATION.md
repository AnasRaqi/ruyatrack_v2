# Configuration Guide

This guide covers all configuration options for the RuyaTracker CLM Enterprise application.

## 📋 Table of Contents

- [Environment Variables](#environment-variables)
- [Vercel Configuration](#vercel-configuration)
- [GitHub Actions Configuration](#github-actions-configuration)
- [Security Configuration](#security-configuration)
- [Performance Configuration](#performance-configuration)
- [Monitoring Configuration](#monitoring-configuration)

## 🔧 Environment Variables

### Application Configuration

```env
# Application Identity
NEXT_PUBLIC_APP_NAME=RuyaTracker CLM Enterprise
NEXT_PUBLIC_APP_VERSION=1.0.0
NEXT_PUBLIC_APP_ENV=production

# Branding
NEXT_PUBLIC_COMPANY_NAME=RuyaTracker
NEXT_PUBLIC_COMPANY_LOGO_URL=/assets/logo.png
NEXT_PUBLIC_FAVICON_URL=/assets/favicon.ico
```

### API Configuration (Future Backend Integration)

```env
# API Endpoints
NEXT_PUBLIC_API_BASE_URL=https://api.ruyatrack.com
NEXT_PUBLIC_API_VERSION=v1
NEXT_PUBLIC_API_TIMEOUT=30000

# Authentication
NEXT_PUBLIC_AUTH_DOMAIN=auth.ruyatrack.com
NEXT_PUBLIC_AUTH_CLIENT_ID=your_auth_client_id
NEXT_PUBLIC_AUTH_AUDIENCE=ruyatrack-clm-api
```

### Analytics and Monitoring

```env
# Google Analytics
NEXT_PUBLIC_GA_TRACKING_ID=GA_MEASUREMENT_ID
NEXT_PUBLIC_GTM_ID=GTM-XXXXXXX

# Vercel Analytics
NEXT_PUBLIC_VERCEL_ANALYTICS=true

# Error Tracking (Sentry)
NEXT_PUBLIC_SENTRY_DSN=https://your-sentry-dsn
NEXT_PUBLIC_SENTRY_ENVIRONMENT=production
```

### Feature Flags

```env
# Feature Toggles
NEXT_PUBLIC_ENABLE_ANALYTICS=true
NEXT_PUBLIC_ENABLE_NOTIFICATIONS=true
NEXT_PUBLIC_ENABLE_INTEGRATIONS=true
NEXT_PUBLIC_ENABLE_AUDIT_TRAIL=true
NEXT_PUBLIC_ENABLE_EXPORT=true

# Experimental Features
NEXT_PUBLIC_ENABLE_BETA_FEATURES=false
NEXT_PUBLIC_ENABLE_DEBUG_MODE=false
```

### Deployment Configuration

```env
# Vercel Configuration
VERCEL_URL=ruyatrack-clm-enterprise.vercel.app
VERCEL_ENV=production
VERCEL_REGION=iad1

# Build Configuration
NODE_ENV=production
BUILD_ENV=production
```

## ⚙️ Vercel Configuration

### vercel.json Structure

```json
{
  "version": 2,
  "name": "ruyatrack-clm-enterprise",
  "framework": null,
  "buildCommand": "npm run build",
  "outputDirectory": "public",
  "installCommand": "npm install",
  "devCommand": "vercel dev",
  "public": true,
  "regions": ["iad1", "fra1", "sin1"],
  "functions": {},
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ],
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
          "value": "default-src 'self' 'unsafe-inline' 'unsafe-eval' data: blob:; img-src 'self' data: blob: https:; font-src 'self' data: https:;"
        }
      ]
    },
    {
      "source": "/static/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=31536000, immutable"
        }
      ]
    }
  ],
  "rewrites": [
    {
      "source": "/(.*)",
      "destination": "/index.html"
    }
  ],
  "cleanUrls": true,
  "trailingSlash": false,
  "env": {
    "NODE_ENV": "production"
  },
  "build": {
    "env": {
      "NODE_ENV": "production"
    }
  }
}
```

### Regional Configuration

```json
{
  "regions": [
    "iad1",  // US East (Virginia)
    "fra1",  // Europe (Frankfurt)
    "sin1"   // Asia (Singapore)
  ]
}
```

### Custom Domains

```json
{
  "alias": [
    "clm.ruyatrack.com",
    "app.ruyatrack.com"
  ]
}
```

## 🔄 GitHub Actions Configuration

### Workflow Variables

Set these in your repository settings under `Settings > Secrets and variables > Actions`:

#### Secrets
```
VERCEL_TOKEN=your_vercel_token
VERCEL_ORG_ID=your_vercel_org_id
VERCEL_PROJECT_ID=your_vercel_project_id
SNYK_TOKEN=your_snyk_token
GITHUB_TOKEN=automatically_provided
```

#### Variables
```
NODE_VERSION=18
VERCEL_CLI_VERSION=latest
LIGHTHOUSE_VERSION=9
```

### Workflow Configuration

```yaml
# .github/workflows/deploy.yml
env:
  NODE_VERSION: 18
  VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
  VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

### Branch Protection Rules

Configure in GitHub repository settings:

```yaml
# Branch protection for main
required_status_checks:
  - lint-and-test
  - security-scan
  - lighthouse-audit
enforce_admins: true
required_pull_request_reviews:
  required_approving_review_count: 1
  dismiss_stale_reviews: true
```

## 🔒 Security Configuration

### Content Security Policy (CSP)

```
default-src 'self' 'unsafe-inline' 'unsafe-eval' data: blob:;
img-src 'self' data: blob: https:;
font-src 'self' data: https:;
connect-src 'self' https://api.ruyatrack.com;
script-src 'self' 'unsafe-inline' 'unsafe-eval';
style-src 'self' 'unsafe-inline';
```

### Security Headers

```json
{
  "X-Content-Type-Options": "nosniff",
  "X-Frame-Options": "DENY",
  "X-XSS-Protection": "1; mode=block",
  "Referrer-Policy": "strict-origin-when-cross-origin",
  "Strict-Transport-Security": "max-age=31536000; includeSubDomains"
}
```

### CORS Configuration

```json
{
  "Access-Control-Allow-Origin": "https://ruyatrack-clm-enterprise.vercel.app",
  "Access-Control-Allow-Methods": "GET, POST, PUT, DELETE, OPTIONS",
  "Access-Control-Allow-Headers": "Content-Type, Authorization"
}
```

## 🚀 Performance Configuration

### Caching Strategy

```json
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
    },
    {
      "source": "/(.*\\.(js|css|png|jpg|jpeg|gif|ico|svg))",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=86400"
        }
      ]
    }
  ]
}
```

### Compression

```json
{
  "compress": true,
  "gzip": true,
  "brotli": true
}
```

### Bundle Optimization

```json
{
  "build": {
    "env": {
      "NODE_ENV": "production",
      "GENERATE_SOURCEMAP": "false",
      "INLINE_RUNTIME_CHUNK": "false"
    }
  }
}
```

## 📊 Monitoring Configuration

### Lighthouse CI Configuration

```json
{
  "ci": {
    "collect": {
      "numberOfRuns": 3,
      "settings": {
        "chromeFlags": "--no-sandbox --disable-dev-shm-usage"
      }
    },
    "assert": {
      "assertions": {
        "categories:performance": ["warn", {"minScore": 0.8}],
        "categories:accessibility": ["error", {"minScore": 0.9}],
        "categories:best-practices": ["warn", {"minScore": 0.8}],
        "categories:seo": ["warn", {"minScore": 0.8}],
        "categories:pwa": "off"
      }
    },
    "upload": {
      "target": "temporary-public-storage"
    }
  }
}
```

### Vercel Analytics

```json
{
  "analytics": {
    "enable": true
  },
  "speed-insights": {
    "enable": true
  }
}
```

### Error Tracking (Sentry)

```javascript
// Future integration
import * as Sentry from "@sentry/browser";

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  environment: process.env.NEXT_PUBLIC_SENTRY_ENVIRONMENT,
  tracesSampleRate: 1.0,
});
```

## 🌍 Multi-Environment Configuration

### Development Environment

```env
# .env.development
NEXT_PUBLIC_APP_ENV=development
NEXT_PUBLIC_API_BASE_URL=http://localhost:3001
NEXT_PUBLIC_ENABLE_DEBUG_MODE=true
```

### Staging Environment

```env
# .env.staging
NEXT_PUBLIC_APP_ENV=staging
NEXT_PUBLIC_API_BASE_URL=https://api-staging.ruyatrack.com
NEXT_PUBLIC_ENABLE_BETA_FEATURES=true
```

### Production Environment

```env
# .env.production
NEXT_PUBLIC_APP_ENV=production
NEXT_PUBLIC_API_BASE_URL=https://api.ruyatrack.com
NEXT_PUBLIC_ENABLE_DEBUG_MODE=false
```

## 🔧 Custom Configuration Options

### Theme Configuration

```javascript
// Future theming support
const themeConfig = {
  primaryColor: '#667eea',
  secondaryColor: '#764ba2',
  backgroundColor: '#f5f6fa',
  textColor: '#2c3e50',
  borderRadius: '8px',
  fontFamily: 'Segoe UI, Tahoma, Geneva, Verdana, sans-serif'
};
```

### Localization Configuration

```javascript
// Future i18n support
const localeConfig = {
  defaultLocale: 'en',
  locales: ['en', 'ar', 'fr'],
  fallbackLocale: 'en',
  dateFormat: 'MM/DD/YYYY',
  timeFormat: '12h',
  currency: 'SAR'
};
```

### Integration Configuration

```javascript
// Future integration support
const integrationConfig = {
  activeDirectory: {
    enabled: false,
    domain: 'ruyatrack.com',
    server: 'ad.ruyatrack.com'
  },
  erp: {
    enabled: false,
    system: 'oracle',
    endpoint: 'https://erp.ruyatrack.com/api'
  },
  notifications: {
    email: {
      enabled: true,
      smtp: 'smtp.ruyatrack.com',
      port: 587
    },
    sms: {
      enabled: false,
      provider: 'twilio'
    }
  }
};
```

## 📝 Configuration Best Practices

### Security Best Practices

1. **Never commit secrets to repository**
2. **Use environment-specific configurations**
3. **Implement proper CSP headers**
4. **Enable security scanning in CI/CD**
5. **Regular security audits**

### Performance Best Practices

1. **Enable compression and caching**
2. **Optimize images and assets**
3. **Minimize bundle size**
4. **Use CDN for static assets**
5. **Monitor Core Web Vitals**

### Maintenance Best Practices

1. **Document all configuration changes**
2. **Use version control for configurations**
3. **Regular backup of configurations**
4. **Test configurations in staging first**
5. **Monitor configuration drift**

---

**Next Steps:**
- [Deployment Guide](DEPLOYMENT.md)
- [Troubleshooting Guide](TROUBLESHOOTING.md)
- [API Integration Guide](API_INTEGRATION.md)

