# Deployment Guide

This guide covers various deployment options for SharedSpace rental platform.

## 🚀 Quick Deploy

### Netlify (Recommended)

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/yourusername/sharedspace-rental-platform)

1. **Connect Repository**
   - Fork this repository
   - Connect your GitHub account to Netlify
   - Select the forked repository

2. **Configure Build Settings**
   ```
   Build command: npm run build
   Publish directory: dist
   ```

3. **Environment Variables**
   - Add your environment variables in Netlify dashboard
   - Copy from `.env.example` and update values

4. **Deploy**
   - Click "Deploy site"
   - Your site will be available at a generated URL

### Vercel

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/yourusername/sharedspace-rental-platform)

1. **Import Project**
   - Connect your GitHub account
   - Import the repository

2. **Configure Project**
   - Framework: Vite
   - Build command: `npm run build`
   - Output directory: `dist`

3. **Environment Variables**
   - Add environment variables in project settings
   - Use `.env.example` as reference

4. **Deploy**
   - Click "Deploy"
   - Access your site at the provided URL

## 🔧 Manual Deployment

### Prerequisites

```bash
# Install dependencies
npm install

# Build the project
npm run build
```

### Static Hosting

The `dist` folder contains all static files needed for deployment.

#### Apache

1. **Upload Files**
   ```bash
   # Upload dist/ contents to your web root
   scp -r dist/* user@server:/var/www/html/
   ```

2. **Configure .htaccess**
   ```apache
   # .htaccess in web root
   RewriteEngine On
   RewriteBase /
   
   # Handle client-side routing
   RewriteCond %{REQUEST_FILENAME} !-f
   RewriteCond %{REQUEST_FILENAME} !-d
   RewriteRule . /index.html [L]
   
   # Security headers
   Header always set X-Frame-Options DENY
   Header always set X-Content-Type-Options nosniff
   Header always set X-XSS-Protection "1; mode=block"
   
   # Cache static assets
   <FilesMatch "\.(js|css|png|jpg|jpeg|gif|ico|svg)$">
     ExpiresActive On
     ExpiresDefault "access plus 1 year"
   </FilesMatch>
   ```

#### Nginx

1. **Upload Files**
   ```bash
   # Upload dist/ contents
   scp -r dist/* user@server:/var/www/sharedspace/
   ```

2. **Configure Nginx**
   ```nginx
   server {
     listen 80;
     server_name yourdomain.com;
     root /var/www/sharedspace;
     index index.html;
   
     # Handle client-side routing
     location / {
       try_files $uri $uri/ /index.html;
     }
   
     # Cache static assets
     location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
       expires 1y;
       add_header Cache-Control "public, immutable";
     }
   
     # Security headers
     add_header X-Frame-Options DENY;
     add_header X-Content-Type-Options nosniff;
     add_header X-XSS-Protection "1; mode=block";
   }
   ```

### Docker Deployment

1. **Create Dockerfile**
   ```dockerfile
   # Build stage
   FROM node:18-alpine as build
   WORKDIR /app
   COPY package*.json ./
   RUN npm ci --only=production
   COPY . .
   RUN npm run build
   
   # Production stage
   FROM nginx:alpine
   COPY --from=build /app/dist /usr/share/nginx/html
   COPY nginx.conf /etc/nginx/nginx.conf
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```

2. **Create nginx.conf**
   ```nginx
   events {
     worker_connections 1024;
   }
   
   http {
     include /etc/nginx/mime.types;
     default_type application/octet-stream;
   
     server {
       listen 80;
       root /usr/share/nginx/html;
       index index.html;
   
       location / {
         try_files $uri $uri/ /index.html;
       }
   
       location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
         expires 1y;
         add_header Cache-Control "public, immutable";
       }
     }
   }
   ```

3. **Build and Run**
   ```bash
   # Build image
   docker build -t sharedspace .
   
   # Run container
   docker run -p 80:80 sharedspace
   ```

### AWS S3 + CloudFront

1. **Create S3 Bucket**
   ```bash
   # Create bucket
   aws s3 mb s3://your-bucket-name
   
   # Upload files
   aws s3 sync dist/ s3://your-bucket-name --delete
   
   # Configure for static website
   aws s3 website s3://your-bucket-name --index-document index.html --error-document index.html
   ```

2. **Create CloudFront Distribution**
   ```json
   {
     "Origins": [{
       "DomainName": "your-bucket-name.s3.amazonaws.com",
       "Id": "S3-your-bucket-name",
       "S3OriginConfig": {
         "OriginAccessIdentity": ""
       }
     }],
     "DefaultCacheBehavior": {
       "TargetOriginId": "S3-your-bucket-name",
       "ViewerProtocolPolicy": "redirect-to-https",
       "Compress": true
     },
     "CustomErrorResponses": [{
       "ErrorCode": 404,
       "ResponseCode": 200,
       "ResponsePagePath": "/index.html"
     }]
   }
   ```

## 🌍 Environment Configuration

### Production Environment Variables

```bash
# Application
VITE_APP_NAME=SharedSpace
VITE_APP_URL=https://yourdomain.com
VITE_APP_VERSION=1.0.0

# API (when backend is ready)
VITE_API_URL=https://api.yourdomain.com
VITE_API_KEY=your_production_api_key

# External Services
VITE_GOOGLE_MAPS_API_KEY=your_google_maps_key
VITE_STRIPE_PUBLISHABLE_KEY=pk_live_your_stripe_key

# Analytics
VITE_GOOGLE_ANALYTICS_ID=GA-XXXXXXXXX

# Feature Flags
VITE_ENABLE_ANALYTICS=true
VITE_ENABLE_PAYMENTS=true
VITE_DEBUG_MODE=false
VITE_MOCK_API=false
```

### Development vs Production

| Setting | Development | Production |
|---------|-------------|------------|
| API URL | localhost:3001 | api.yourdomain.com |
| Debug Mode | true | false |
| Mock API | true | false |
| Analytics | false | true |
| Source Maps | true | false |

## 🔒 Security Configuration

### SSL/TLS Certificate

Most hosting providers offer free SSL certificates:

- **Netlify**: Automatic Let's Encrypt certificates
- **Vercel**: Automatic SSL for custom domains
- **Cloudflare**: Free SSL with additional security features

### Security Headers

Ensure these headers are configured:

```
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
X-XSS-Protection: 1; mode=block
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'
```

## 📊 Performance Optimization

### Build Optimization

```bash
# Analyze bundle size
npm run analyze

# Check for unused dependencies
npx depcheck

# Optimize images
npx imagemin src/assets/* --out-dir=dist/assets
```

### CDN Configuration

Configure your CDN to:

- Cache static assets for 1 year
- Compress all text-based files
- Use HTTP/2 push for critical resources
- Enable Brotli compression

### Monitoring

Set up monitoring for:

- **Uptime**: Use services like Pingdom or UptimeRobot
- **Performance**: Google PageSpeed Insights, GTmetrix
- **Errors**: Sentry or LogRocket for error tracking
- **Analytics**: Google Analytics or Plausible

## 🚨 Troubleshooting

### Common Issues

1. **404 on Refresh**
   - Ensure client-side routing is configured
   - Check `.htaccess` or nginx configuration

2. **Environment Variables Not Working**
   - Verify variables start with `VITE_`
   - Check build logs for missing variables

3. **Build Failures**
   - Clear node_modules and reinstall
   - Check Node.js version compatibility

4. **Performance Issues**
   - Enable compression (gzip/brotli)
   - Optimize images and assets
   - Use CDN for static files

### Debug Commands

```bash
# Check build output
npm run build -- --debug

# Analyze bundle
npm run analyze

# Test production build locally
npm run preview

# Check for security vulnerabilities
npm audit

# Validate HTML
npx html-validate dist/index.html
```

## 📞 Support

For deployment issues:

1. Check the [troubleshooting section](#-troubleshooting)
2. Search [existing issues](https://github.com/yourusername/sharedspace-rental-platform/issues)
3. Create a new issue with deployment details
4. Contact support at deploy@sharedspace.com

---

**Happy Deploying! 🚀**