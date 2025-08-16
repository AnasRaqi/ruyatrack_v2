# Troubleshooting Guide

This guide helps you diagnose and resolve common issues with the RuyaTracker CLM Enterprise deployment and operation.

## 📋 Table of Contents

- [Deployment Issues](#deployment-issues)
- [Runtime Issues](#runtime-issues)
- [Performance Issues](#performance-issues)
- [Security Issues](#security-issues)
- [CI/CD Pipeline Issues](#cicd-pipeline-issues)
- [Browser Compatibility Issues](#browser-compatibility-issues)

## 🚀 Deployment Issues

### Build Failures

#### Issue: Build fails with "Command failed with exit code 1"

**Symptoms:**
```
Error: Command "npm run build" exited with 1
```

**Diagnosis:**
```bash
# Check build logs locally
npm run build

# Check for syntax errors
npm run lint

# Verify dependencies
npm audit
```

**Solutions:**
1. **Fix syntax errors in code**
2. **Update dependencies:**
   ```bash
   npm update
   npm audit fix
   ```
3. **Clear cache and reinstall:**
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   ```

#### Issue: Vercel deployment timeout

**Symptoms:**
```
Error: Build exceeded maximum duration of 45 seconds
```

**Solutions:**
1. **Optimize build process:**
   ```json
   // package.json
   {
     "scripts": {
       "build": "npm run copy-assets --silent"
     }
   }
   ```
2. **Reduce bundle size**
3. **Use Vercel Pro for longer build times**

### Environment Variable Issues

#### Issue: Environment variables not loading

**Symptoms:**
- Features not working as expected
- API calls failing
- Configuration not applied

**Diagnosis:**
```bash
# Check environment variables in Vercel
vercel env ls

# Pull environment variables locally
vercel env pull .env.local

# Verify variables in browser console
console.log(process.env.NEXT_PUBLIC_APP_NAME);
```

**Solutions:**
1. **Verify variable names have NEXT_PUBLIC_ prefix for client-side access**
2. **Check Vercel dashboard environment variables**
3. **Redeploy after adding variables:**
   ```bash
   vercel --prod
   ```

### Domain Configuration Issues

#### Issue: Custom domain not working

**Symptoms:**
- Domain shows "This site can't be reached"
- SSL certificate errors
- Redirect loops

**Diagnosis:**
```bash
# Check DNS configuration
nslookup your-domain.com

# Check SSL certificate
openssl s_client -connect your-domain.com:443 -servername your-domain.com

# Check domain status in Vercel
vercel domains ls
```

**Solutions:**
1. **Configure DNS correctly:**
   ```
   Type: CNAME
   Name: www (or @)
   Value: cname.vercel-dns.com
   ```
2. **Wait for DNS propagation (up to 48 hours)**
3. **Verify domain ownership in Vercel dashboard**

## 🔧 Runtime Issues

### Authentication Issues

#### Issue: Login not working

**Symptoms:**
- Login form doesn't respond
- Invalid credentials error for valid accounts
- Session not persisting

**Diagnosis:**
```javascript
// Check browser console for errors
console.log('Current user:', currentUser);
console.log('User roles:', userRoles);

// Check localStorage
console.log('Session:', localStorage.getItem('clm_session'));
```

**Solutions:**
1. **Clear browser cache and localStorage:**
   ```javascript
   localStorage.clear();
   sessionStorage.clear();
   ```
2. **Check demo credentials are correct**
3. **Verify JavaScript is enabled**
4. **Check for browser extensions blocking scripts**

#### Issue: Session not persisting across page refreshes

**Symptoms:**
- User gets logged out on page refresh
- Login state not maintained

**Solutions:**
1. **Check localStorage implementation:**
   ```javascript
   // Verify session storage
   function checkSession() {
     const session = localStorage.getItem('clm_session');
     if (session) {
       try {
         currentUser = JSON.parse(session);
         return true;
       } catch (e) {
         localStorage.removeItem('clm_session');
       }
     }
     return false;
   }
   ```

### Navigation Issues

#### Issue: Navigation not working

**Symptoms:**
- Clicking navigation items doesn't change sections
- JavaScript errors in console
- Sections not displaying

**Diagnosis:**
```javascript
// Check for JavaScript errors
console.error('Navigation error');

// Verify navigation function
function showSection(sectionId) {
  console.log('Showing section:', sectionId);
}
```

**Solutions:**
1. **Check for JavaScript syntax errors**
2. **Verify event listeners are attached:**
   ```javascript
   document.addEventListener('DOMContentLoaded', function() {
     // Navigation setup
   });
   ```
3. **Clear browser cache**

### Data Issues

#### Issue: Data not persisting

**Symptoms:**
- Changes disappear on page refresh
- CRUD operations not working
- Data resets to default values

**Solutions:**
1. **Implement proper data persistence:**
   ```javascript
   // Save to localStorage
   function saveData(key, data) {
     localStorage.setItem(key, JSON.stringify(data));
   }
   
   // Load from localStorage
   function loadData(key) {
     const data = localStorage.getItem(key);
     return data ? JSON.parse(data) : null;
   }
   ```

## 🚀 Performance Issues

### Slow Loading Times

#### Issue: Application loads slowly

**Symptoms:**
- Long initial load times
- Poor Lighthouse scores
- Slow Time to Interactive (TTI)

**Diagnosis:**
```bash
# Run Lighthouse audit
npx lighthouse https://your-domain.com --output=html --output-path=./lighthouse-report.html

# Check bundle size
npm run build
ls -la public/
```

**Solutions:**
1. **Optimize images:**
   ```bash
   # Compress images
   npm install -g imagemin-cli
   imagemin src/assets/*.png --out-dir=public/assets/
   ```

2. **Enable compression in Vercel:**
   ```json
   // vercel.json
   {
     "headers": [
       {
         "source": "/(.*)",
         "headers": [
           {
             "key": "Content-Encoding",
             "value": "gzip"
           }
         ]
       }
     ]
   }
   ```

3. **Implement lazy loading:**
   ```javascript
   // Lazy load sections
   function loadSection(sectionId) {
     const section = document.getElementById(sectionId);
     if (section && !section.dataset.loaded) {
       // Load section content
       section.dataset.loaded = 'true';
     }
   }
   ```

### Memory Issues

#### Issue: High memory usage

**Symptoms:**
- Browser becomes unresponsive
- Tab crashes
- Performance degradation over time

**Solutions:**
1. **Implement proper cleanup:**
   ```javascript
   // Clean up event listeners
   function cleanup() {
     document.removeEventListener('click', handleClick);
   }
   
   // Clean up intervals
   clearInterval(intervalId);
   ```

2. **Optimize data structures:**
   ```javascript
   // Use efficient data structures
   const dataMap = new Map();
   const dataSet = new Set();
   ```

## 🔒 Security Issues

### CSP Violations

#### Issue: Content Security Policy violations

**Symptoms:**
- Features not working
- Console errors about blocked resources
- Inline scripts not executing

**Diagnosis:**
```javascript
// Check console for CSP violations
// Look for errors like: "Refused to execute inline script"
```

**Solutions:**
1. **Update CSP headers:**
   ```json
   // vercel.json
   {
     "headers": [
       {
         "source": "/(.*)",
         "headers": [
           {
             "key": "Content-Security-Policy",
             "value": "default-src 'self' 'unsafe-inline' 'unsafe-eval' data: blob:;"
           }
         ]
       }
     ]
   }
   ```

2. **Move inline scripts to external files**
3. **Use nonce or hash for specific scripts**

### HTTPS Issues

#### Issue: Mixed content warnings

**Symptoms:**
- Browser warnings about insecure content
- Some resources not loading
- SSL certificate errors

**Solutions:**
1. **Ensure all resources use HTTPS:**
   ```html
   <!-- Change from http:// to https:// -->
   <script src="https://cdn.example.com/script.js"></script>
   ```

2. **Use protocol-relative URLs:**
   ```html
   <script src="//cdn.example.com/script.js"></script>
   ```

## 🔄 CI/CD Pipeline Issues

### GitHub Actions Failures

#### Issue: Workflow fails to run

**Symptoms:**
- Actions not triggering
- Build failures in CI
- Deployment not happening

**Diagnosis:**
```bash
# Check workflow syntax
yamllint .github/workflows/deploy.yml

# Check GitHub Actions logs
# Go to repository > Actions tab > Select failed workflow
```

**Solutions:**
1. **Verify workflow syntax:**
   ```yaml
   # .github/workflows/deploy.yml
   name: Deploy to Vercel
   on:
     push:
       branches: [ main ]
   ```

2. **Check secrets configuration:**
   ```bash
   # Verify secrets are set in repository settings
   # Settings > Secrets and variables > Actions
   ```

3. **Update action versions:**
   ```yaml
   - uses: actions/checkout@v4  # Use latest version
   - uses: actions/setup-node@v4
   ```

### Vercel Integration Issues

#### Issue: Vercel deployment fails in CI

**Symptoms:**
- CI passes but Vercel deployment fails
- Authentication errors
- Project not found errors

**Solutions:**
1. **Verify Vercel tokens:**
   ```bash
   # Check token permissions
   vercel whoami --token=$VERCEL_TOKEN
   ```

2. **Update project configuration:**
   ```bash
   # Re-link project
   vercel link --yes
   ```

## 🌐 Browser Compatibility Issues

### JavaScript Errors in Older Browsers

#### Issue: Application not working in older browsers

**Symptoms:**
- Blank page in Internet Explorer
- JavaScript errors in older Chrome/Firefox
- Features not working on mobile

**Solutions:**
1. **Add polyfills:**
   ```html
   <!-- Add to head section -->
   <script src="https://polyfill.io/v3/polyfill.min.js"></script>
   ```

2. **Use compatible JavaScript:**
   ```javascript
   // Instead of arrow functions
   function handleClick() {
     // Compatible with older browsers
   }
   
   // Instead of const/let
   var data = [];
   ```

### Mobile Responsiveness Issues

#### Issue: Application not working on mobile

**Symptoms:**
- Layout broken on mobile
- Touch events not working
- Text too small to read

**Solutions:**
1. **Add viewport meta tag:**
   ```html
   <meta name="viewport" content="width=device-width, initial-scale=1.0">
   ```

2. **Use responsive CSS:**
   ```css
   @media (max-width: 768px) {
     .container {
       padding: 10px;
     }
   }
   ```

3. **Add touch event support:**
   ```javascript
   // Support both mouse and touch events
   element.addEventListener('click', handleClick);
   element.addEventListener('touchstart', handleClick);
   ```

## 🆘 Getting Help

### Diagnostic Commands

```bash
# Check application status
curl -I https://your-domain.com

# Check DNS resolution
nslookup your-domain.com

# Check SSL certificate
openssl s_client -connect your-domain.com:443

# Check Vercel deployment status
vercel ls

# Check build logs
vercel logs

# Test local build
npm run build && npm run start
```

### Log Analysis

```javascript
// Enable debug logging
localStorage.setItem('debug', 'true');

// Check browser console
console.log('Debug info:', {
  userAgent: navigator.userAgent,
  currentUser: currentUser,
  localStorage: localStorage.getItem('clm_session')
});
```

### Support Channels

1. **GitHub Issues**
   - Create detailed issue with reproduction steps
   - Include browser version and error messages
   - Attach relevant logs and screenshots

2. **Vercel Support**
   - Check Vercel status page
   - Contact Vercel support for platform issues
   - Review Vercel documentation

3. **Community Support**
   - Stack Overflow with relevant tags
   - GitHub Discussions
   - Developer forums

### Creating Effective Bug Reports

Include the following information:

1. **Environment Details:**
   - Browser version
   - Operating system
   - Device type (desktop/mobile)
   - Network conditions

2. **Reproduction Steps:**
   - Exact steps to reproduce
   - Expected behavior
   - Actual behavior
   - Screenshots/videos if applicable

3. **Technical Details:**
   - Console error messages
   - Network requests (if relevant)
   - Local storage contents
   - Relevant configuration

4. **Attempted Solutions:**
   - What you've already tried
   - Workarounds that work/don't work
   - Related issues or documentation

---

**Additional Resources:**
- [Deployment Guide](DEPLOYMENT.md)
- [Configuration Guide](CONFIGURATION.md)
- [Vercel Documentation](https://vercel.com/docs)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)

