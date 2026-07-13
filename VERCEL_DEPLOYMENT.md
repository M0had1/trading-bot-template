# Vercel Deployment Guide

## Project Overview
This is a React-based trading bot template using RSBuild as the build tool. It's optimized for deployment on Vercel.

## Prerequisites
- Vercel account
- GitHub repository connection (recommended)
- CLIENT_ID from Deriv (e.g., `33BeITFXk8T0IJ8CSLrkB`)

## Deployment Steps

### 1. Connect to Vercel
Option A (Recommended):
```bash
npm install -g vercel
vercel link
```

Option B: Connect via Vercel Dashboard
- Go to https://vercel.com/dashboard
- Click "New Project"
- Select your GitHub repository (M0had1/trading-bot-template)

### 2. Set Environment Variables

On Vercel Dashboard:
1. Navigate to your project settings
2. Go to "Environment Variables"
3. Add the following variables:

**Required:**
- `CLIENT_ID` = `33BeITFXk8T0IJ8CSLrkB` (OAuth client ID from Deriv)

**Optional:**
- `APP_ENV` = `production`
- `NODE_ENV` = `production` (auto-set by Vercel)

### 3. Build Configuration

**Build Command:** (Auto-detected from vercel.json)
```bash
node ./node_modules/@rsbuild/core/bin/rsbuild.js build
```

**Install Command:**
```bash
npm install --legacy-peer-deps
```

**Output Directory:** `dist`

### 4. Deployment Methods

#### Method A: Git Push (Recommended)
```bash
git add .
git commit -m "Setup for Vercel deployment"
git push origin master
```
Vercel automatically deploys on every push to connected branches.

#### Method B: Manual Deploy
```bash
vercel --prod
```

#### Method C: Using Vercel CLI
```bash
vercel deploy --prod
```

### 5. Verify Deployment

After deployment:
1. Check the Vercel dashboard for build status
2. Visit your deployment URL (provided in dashboard)
3. Verify the trading bot loads correctly
4. Click "Log in" to test OAuth flow with CLIENT_ID

### 6. Custom Domain (Optional)

1. Go to project settings → "Domains"
2. Add your custom domain (e.g., tradingbot.yourdomain.com)
3. Follow DNS configuration instructions
4. Update Deriv OAuth redirect URI:
   - Old: `https://<vercel-url>`
   - New: `https://<custom-domain>`

## Build Process Details

### What Happens During Build
1. Dependencies installed with `npm install --legacy-peer-deps`
2. RSBuild compiles React + TypeScript to `/dist`
3. SmartCharts library assets copied to `/dist/assets`
4. CSS, JavaScript, and HTML bundled for production
5. Output ready to serve as static SPA

### Build Output
- Main HTML: `/dist/index.html`
- JavaScript: `/dist/static/js/`
- CSS: `/dist/static/css/`
- Assets: `/dist/assets/`
- SmartCharts: `/dist/js/smartcharts/`

## Troubleshooting

### Build Fails with "rsbuild: command not found"
✅ **Fixed in vercel.json** - Uses direct node path: `node ./node_modules/@rsbuild/core/bin/rsbuild.js build`

### CLIENT_ID Not Working
1. Verify CLIENT_ID is set in Environment Variables
2. Check that OAuth URI in Deriv matches deployment domain
3. Ensure CLIENT_ID is being injected (check browser DevTools → Network → XHR)

### Redirects Not Working
✅ **Configured in vercel.json** - SPA redirect to index.html for all routes

### Build Times Out
- Current estimated: ~2-3 minutes
- If exceeded, check bundle size: `npm run build:analyze`
- Consider splitting large components using React.lazy()

## Performance Optimization

### Current Metrics
- Bundle Size: Optimized with code splitting
- JavaScript: ~300KB gzipped (main bundle)
- CSS: ~80KB gzipped
- SmartCharts: Lazy-loaded on demand

### Vercel Optimizations Applied
- Automatic compression
- Edge caching for static assets
- Image optimization for PNG/SVG
- Code splitting enabled
- `.vercelignore` configured to skip dev files

## Environment Variable Injection

The app receives environment variables at build time:
```javascript
process.env.CLIENT_ID          // OAuth client ID
process.env.APP_ENV            // Environment name
process.env.NODE_ENV           // Always 'production' on Vercel
process.env.DERIV_WS_APP_ID    // WebSocket app ID (default: 36300)
```

These are baked into the JavaScript bundle during build.

## Local Testing of Production Build

```bash
# Build locally
npm run build

# Test built version
npm run serve
# Opens http://localhost:8443
```

## Rollback

If deployment has issues:
1. Vercel Dashboard → Deployments
2. Find previous working deployment
3. Click the deployment → Promote to Production

## Support

- Vercel Docs: https://vercel.com/docs
- RSBuild Docs: https://rsbuild.dev
- Deriv API: https://api.deriv.com/docs
- Project README: README.md

---

**Last Updated:** 2026-07-12
**Status:** Ready for Production
