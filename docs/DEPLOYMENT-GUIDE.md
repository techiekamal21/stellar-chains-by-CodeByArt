# Stellar Chains - Deployment Guide

## Overview

Stellar Chains is designed for flexible deployment across multiple platforms. This guide covers various deployment scenarios and best practices.

## Deployment Options

### 1. Standalone HTML Deployment

#### Description
Deploy the game as a single HTML file that can be opened directly in a browser or hosted on any web server.

#### Steps
1. Use the `index.html` file from the project root
2. Upload to any web server or CDN
3. Access via direct URL

#### Advantages
- ✅ Simplest deployment method
- ✅ No build process required
- ✅ Works on any hosting platform
- ✅ Can be opened locally

#### Use Cases
- Quick testing
- Personal websites
- File sharing
- Offline play

---

### 2. Static Website Hosting

#### Supported Platforms
- GitHub Pages
- Netlify
- Vercel
- AWS S3 + CloudFront
- Google Cloud Storage
- Azure Static Web Apps

#### GitHub Pages Deployment

**Step 1: Build the project**
```bash
npm install
npm run build
```

**Step 2: Configure GitHub Pages**
1. Push code to GitHub repository
2. Go to Settings → Pages
3. Select branch (main/master)
4. Select folder (/root or /docs)
5. Save

**Step 3: Access**
```
https://[username].github.io/[repository-name]/
```

#### Netlify Deployment

**Option A: Drag and Drop**
1. Build project: `npm run build`
2. Go to https://app.netlify.com/drop
3. Drag `dist` folder
4. Get instant URL

**Option B: Git Integration**
1. Connect GitHub repository
2. Build command: `npm run build`
3. Publish directory: `dist`
4. Deploy

**netlify.toml** (optional):
```toml
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

#### Vercel Deployment

**Step 1: Install Vercel CLI**
```bash
npm install -g vercel
```

**Step 2: Deploy**
```bash
vercel
```

**Step 3: Follow prompts**
- Link to existing project or create new
- Configure build settings
- Deploy

**vercel.json** (optional):
```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "vite"
}
```

---

### 3. WordPress Plugin Integration

#### Overview
Stellar Chains can be embedded in WordPress sites using a custom plugin or page builder.

#### Method A: Custom Plugin

**Create Plugin Structure**
```
stellar-chains-wp/
├── stellar-chains-game.php
├── assets/
│   ├── index.html
│   └── game.js
└── readme.txt
```

**stellar-chains-game.php**:
```php
<?php
/**
 * Plugin Name: Stellar Chains Game
 * Description: Addictive HTML5 puzzle game
 * Version: 1.0.0
 * Author: codebyart
 */

function stellar_chains_enqueue_scripts() {
    wp_enqueue_script(
        'stellar-chains-game',
        plugins_url('assets/game.js', __FILE__),
        array(),
        '1.0.0',
        true
    );
}
add_action('wp_enqueue_scripts', 'stellar_chains_enqueue_scripts');

function stellar_chains_shortcode() {
    ob_start();
    include plugin_dir_path(__FILE__) . 'assets/index.html';
    return ob_get_clean();
}
add_shortcode('stellar_chains', 'stellar_chains_shortcode');
```

**Usage**:
```
[stellar_chains]
```

#### Method B: Iframe Embed

**Step 1: Host game on external server**
```html
https://yourdomain.com/stellar-chains/
```

**Step 2: Add to WordPress page**
```html
<iframe 
  src="https://yourdomain.com/stellar-chains/" 
  width="100%" 
  height="700px" 
  frameborder="0"
  style="max-width: 600px; margin: 0 auto; display: block;">
</iframe>
```

#### Method C: Direct HTML Embed

**Step 1: Copy game code from index.html**

**Step 2: Add to WordPress page**
1. Edit page in WordPress
2. Switch to "Code Editor" or "HTML" mode
3. Paste game code
4. Publish

**Note**: Ensure WordPress theme doesn't conflict with game styles.

---

### 4. CDN Deployment

#### AWS CloudFront + S3

**Step 1: Create S3 Bucket**
```bash
aws s3 mb s3://stellar-chains-game
```

**Step 2: Build and Upload**
```bash
npm run build
aws s3 sync dist/ s3://stellar-chains-game --acl public-read
```

**Step 3: Create CloudFront Distribution**
1. Origin: S3 bucket
2. Default root object: index.html
3. Enable compression
4. Deploy

**Step 4: Access**
```
https://[distribution-id].cloudfront.net/
```

#### Cloudflare Pages

**Step 1: Connect Repository**
1. Go to Cloudflare Pages
2. Connect GitHub account
3. Select repository

**Step 2: Configure Build**
- Build command: `npm run build`
- Build output: `dist`
- Framework: Vite

**Step 3: Deploy**
- Automatic deployment on git push
- Preview deployments for PRs

---

### 5. Mobile App Wrapper

#### Cordova/PhoneGap

**Step 1: Install Cordova**
```bash
npm install -g cordova
```

**Step 2: Create Project**
```bash
cordova create StellarChains com.codebyart.stellarchains "Stellar Chains"
cd StellarChains
```

**Step 3: Add Platforms**
```bash
cordova platform add android
cordova platform add ios
```

**Step 4: Copy Game Files**
```bash
cp -r ../stellar-chains/dist/* www/
```

**Step 5: Build**
```bash
cordova build android
cordova build ios
```

#### Capacitor

**Step 1: Install Capacitor**
```bash
npm install @capacitor/core @capacitor/cli
npx cap init
```

**Step 2: Add Platforms**
```bash
npx cap add android
npx cap add ios
```

**Step 3: Build and Sync**
```bash
npm run build
npx cap sync
```

**Step 4: Open in IDE**
```bash
npx cap open android
npx cap open ios
```

---

## Environment Configuration

### Development Environment

**.env.local**:
```bash
GEMINI_API_KEY=your_api_key_here
```

**Note**: The current implementation doesn't use the Gemini API, but the configuration is present for future AI features.

### Production Environment

**Remove Development Dependencies**:
```bash
npm prune --production
```

**Optimize Build**:
```javascript
// vite.config.ts
export default defineConfig({
  build: {
    minify: 'terser',
    terserOptions: {
      compress: {
        drop_console: true,
      },
    },
  },
});
```

---

## Performance Optimization

### Build Optimization

**1. Code Splitting**
```javascript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
        },
      },
    },
  },
});
```

**2. Asset Optimization**
- Minify JavaScript
- Compress images (if any added)
- Enable gzip/brotli compression

**3. Caching Strategy**
```nginx
# nginx.conf
location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}
```

### CDN Configuration

**CloudFlare Settings**:
- Auto Minify: JS, CSS, HTML
- Brotli compression: Enabled
- Caching level: Standard
- Browser cache TTL: 1 year

**AWS CloudFront**:
```json
{
  "Compress": true,
  "ViewerProtocolPolicy": "redirect-to-https",
  "DefaultTTL": 86400,
  "MaxTTL": 31536000
}
```

---

## Security Considerations

### Content Security Policy

**Recommended CSP Header**:
```html
<meta http-equiv="Content-Security-Policy" 
      content="default-src 'self'; 
               script-src 'self' 'unsafe-inline' https://cdn.tailwindcss.com https://aistudiocdn.com; 
               style-src 'self' 'unsafe-inline' https://cdn.tailwindcss.com; 
               img-src 'self' data:; 
               connect-src 'self';">
```

### HTTPS Enforcement

**Always use HTTPS in production**:
```nginx
# nginx.conf
server {
    listen 80;
    server_name yourdomain.com;
    return 301 https://$server_name$request_uri;
}
```

### CORS Configuration

**If hosting game separately from main site**:
```javascript
// Express.js example
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', 'https://yourdomain.com');
  res.header('Access-Control-Allow-Methods', 'GET');
  next();
});
```

---

## Monitoring and Analytics

### Google Analytics Integration

**Add to index.html**:
```html
<script async src="https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'GA_MEASUREMENT_ID');
</script>
```

### Custom Event Tracking

**Track game events**:
```javascript
// Add to game code
function trackEvent(action, label, value) {
  if (typeof gtag !== 'undefined') {
    gtag('event', action, {
      event_category: 'Game',
      event_label: label,
      value: value
    });
  }
}

// Usage examples
trackEvent('game_start', 'Level 1', 0);
trackEvent('level_complete', 'Level 1', score);
trackEvent('game_over', 'Final Score', finalScore);
```

---

## Troubleshooting

### Common Issues

**Issue 1: Game not loading**
- Check browser console for errors
- Verify all assets are accessible
- Check CSP headers

**Issue 2: Audio not working**
- User interaction required to initialize audio
- Check browser audio permissions
- Verify Web Audio API support

**Issue 3: Touch controls not working**
- Ensure preventDefault() on touch events
- Check touch event listeners
- Test on actual mobile device

**Issue 4: Performance issues**
- Check canvas size (should be reasonable)
- Verify requestAnimationFrame is used
- Monitor particle count

### Browser Compatibility Testing

**Test on**:
- Chrome/Edge (latest)
- Firefox (latest)
- Safari (latest)
- Mobile Safari (iOS)
- Chrome Mobile (Android)

**Minimum Requirements**:
- HTML5 Canvas support
- ES6 JavaScript support
- Web Audio API (optional)
- LocalStorage support

---

## Maintenance

### Version Updates

**Update version in**:
1. package.json
2. Plugin header (if WordPress)
3. metadata.json
4. README.md

### Backup Strategy

**Regular backups of**:
- Source code (Git)
- Build artifacts
- Configuration files
- High score data (if server-side)

### Update Checklist

- [ ] Test all game mechanics
- [ ] Verify audio functionality
- [ ] Check responsive design
- [ ] Test on multiple browsers
- [ ] Validate performance
- [ ] Update documentation
- [ ] Create changelog entry
- [ ] Tag release in Git
- [ ] Deploy to staging
- [ ] Deploy to production

---

## Support and Resources

### Documentation
- [GAME-OVERVIEW.md](./GAME-OVERVIEW.md)
- [TECHNICAL-ARCHITECTURE.md](./TECHNICAL-ARCHITECTURE.md)
- [GAME-MECHANICS.md](./GAME-MECHANICS.md)
- [API-REFERENCE.md](./API-REFERENCE.md)

### Contact
- Author: codebyart
- Website: [Your website]
- Email: [Your email]
- GitHub: [Repository URL]
