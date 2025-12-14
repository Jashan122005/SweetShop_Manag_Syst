# Deployment Guide

This guide covers deploying the Sweet Shop Management System to production.

## Prerequisites

- Supabase project set up and configured
- Git repository hosted on GitHub, GitLab, or Bitbucket
- Edge Functions deployed to Supabase
- Production environment variables ready

## Deployment Options

### Option 1: Vercel (Recommended)

Vercel provides the best integration with Vite and React applications.

#### Steps:

1. **Prepare for Deployment**

Create a `vercel.json` file:
```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "devCommand": "npm run dev",
  "installCommand": "npm install"
}
```

2. **Install Vercel CLI**
```bash
npm i -g vercel
```

3. **Login to Vercel**
```bash
vercel login
```

4. **Deploy**
```bash
vercel
```

5. **Set Environment Variables**

In Vercel Dashboard:
- Go to Project Settings > Environment Variables
- Add:
  - `VITE_SUPABASE_URL`: Your Supabase project URL
  - `VITE_SUPABASE_ANON_KEY`: Your Supabase anon key

6. **Deploy to Production**
```bash
vercel --prod
```

#### Automatic Deployments

Connect your GitHub repository for automatic deployments:

1. Go to [vercel.com](https://vercel.com)
2. Click "Import Project"
3. Select your repository
4. Configure environment variables
5. Deploy

Every push to main will automatically deploy to production!

### Option 2: Netlify

1. **Create `netlify.toml`**
```toml
[build]
  command = "npm run build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

2. **Deploy via CLI**
```bash
npm install -g netlify-cli
netlify login
netlify init
netlify deploy --prod
```

3. **Or Connect GitHub**
- Go to [netlify.com](https://netlify.com)
- Click "New site from Git"
- Select repository
- Configure build settings
- Add environment variables
- Deploy

### Option 3: AWS Amplify

1. **Create `amplify.yml`**
```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm install
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: dist
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

2. **Deploy**
- Go to AWS Amplify Console
- Connect repository
- Configure build settings
- Add environment variables
- Deploy

### Option 4: Cloudflare Pages

1. **Connect Repository**
- Go to Cloudflare Pages dashboard
- Click "Create a project"
- Connect your Git repository

2. **Configure Build**
- Build command: `npm run build`
- Build output directory: `dist`
- Add environment variables

3. **Deploy**
- Click "Save and Deploy"

## Pre-Deployment Checklist

### Code Quality
- [ ] All tests passing
- [ ] No console errors in production build
- [ ] Linter warnings resolved
- [ ] TypeScript compilation successful
- [ ] Build completes without errors

### Security
- [ ] Environment variables not committed to Git
- [ ] RLS policies tested and working
- [ ] Authentication flows tested
- [ ] API endpoints secured
- [ ] No sensitive data in client code

### Performance
- [ ] Images optimized
- [ ] Bundle size acceptable (<500KB)
- [ ] Lazy loading implemented where appropriate
- [ ] API calls optimized

### Testing
- [ ] Unit tests passing
- [ ] Integration tests passing
- [ ] Manual testing completed
- [ ] Cross-browser testing done
- [ ] Mobile responsiveness verified

### Documentation
- [ ] README.md complete
- [ ] API documentation up to date
- [ ] Setup instructions accurate
- [ ] Environment variables documented

## Post-Deployment Steps

### 1. Verify Deployment

```bash
# Check if site is accessible
curl -I https://your-site.vercel.app

# Test API endpoints
curl https://your-site.vercel.app/api/health
```

### 2. Test Critical Paths

- [ ] User registration works
- [ ] User login works
- [ ] Sweets are displayed
- [ ] Search functionality works
- [ ] Admin features work (if admin)
- [ ] Purchase flow works
- [ ] Restock works (if admin)

### 3. Monitor Performance

Use these tools to monitor your deployment:
- [Lighthouse](https://developers.google.com/web/tools/lighthouse)
- [WebPageTest](https://www.webpagetest.org/)
- [GTmetrix](https://gtmetrix.com/)

### 4. Set Up Error Monitoring (Optional)

Consider integrating error monitoring:
- [Sentry](https://sentry.io/)
- [LogRocket](https://logrocket.com/)
- [Rollbar](https://rollbar.com/)

## Environment Variables

### Production Environment Variables

```env
# Supabase
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-production-anon-key

# Optional: Analytics
VITE_GA_TRACKING_ID=your-ga-tracking-id
```

### Managing Environment Variables

**Vercel:**
```bash
vercel env add VITE_SUPABASE_URL production
vercel env add VITE_SUPABASE_ANON_KEY production
```

**Netlify:**
```bash
netlify env:set VITE_SUPABASE_URL "your-url" --context production
netlify env:set VITE_SUPABASE_ANON_KEY "your-key" --context production
```

## Custom Domain Setup

### Vercel

1. Go to Project Settings > Domains
2. Add your domain
3. Update DNS records as instructed
4. Wait for DNS propagation (can take up to 48 hours)

### Netlify

1. Go to Domain Settings
2. Add custom domain
3. Update nameservers or add DNS records
4. Enable HTTPS (automatic with Let's Encrypt)

## CI/CD Pipeline

### GitHub Actions Example

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build
        run: npm run build
        env:
          VITE_SUPABASE_URL: ${{ secrets.VITE_SUPABASE_URL }}
          VITE_SUPABASE_ANON_KEY: ${{ secrets.VITE_SUPABASE_ANON_KEY }}

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

## Troubleshooting

### Issue: Environment variables not working
**Solution:**
- Ensure variables start with `VITE_` prefix
- Rebuild after changing variables
- Check deployment platform's environment variable settings

### Issue: 404 on page refresh
**Solution:**
- Add redirect rules to handle client-side routing
- For Vercel: Create `vercel.json` with rewrites
- For Netlify: Add `_redirects` file or use `netlify.toml`

### Issue: API calls failing
**Solution:**
- Check CORS configuration
- Verify Edge Functions are deployed
- Confirm Supabase URL is correct
- Check browser console for errors

### Issue: Slow load times
**Solution:**
- Enable compression
- Optimize images
- Use CDN
- Enable caching headers
- Code split large bundles

## Performance Optimization

### Build Optimization

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          'react-vendor': ['react', 'react-dom'],
          'supabase': ['@supabase/supabase-js'],
        },
      },
    },
    chunkSizeWarningLimit: 1000,
  },
});
```

### Enable Compression

Most platforms enable this by default, but verify:
- Gzip compression enabled
- Brotli compression enabled
- Cache headers configured

## Monitoring & Analytics

### Add Google Analytics (Optional)

1. Create GA4 property
2. Get tracking ID
3. Add to environment variables
4. Install analytics package:

```bash
npm install react-ga4
```

5. Initialize in your app:

```typescript
import ReactGA from 'react-ga4';

ReactGA.initialize(import.meta.env.VITE_GA_TRACKING_ID);
```

## Backup Strategy

### Database Backups

Supabase provides automatic backups, but you can also:

1. **Manual Backup:**
```sql
-- Export all data
COPY (SELECT * FROM sweets) TO '/tmp/sweets_backup.csv' CSV HEADER;
COPY (SELECT * FROM profiles) TO '/tmp/profiles_backup.csv' CSV HEADER;
```

2. **Automated Backups:**
- Use Supabase's Point-in-Time Recovery
- Set up daily backups via Supabase dashboard

## Rollback Strategy

If deployment fails:

```bash
# Vercel - rollback to previous deployment
vercel rollback

# Or redeploy previous commit
git revert HEAD
git push origin main
```

## Support & Resources

- [Vercel Documentation](https://vercel.com/docs)
- [Netlify Documentation](https://docs.netlify.com)
- [Supabase Documentation](https://supabase.com/docs)
- [Vite Deployment Guide](https://vitejs.dev/guide/static-deploy.html)

## Final Checklist

Before marking deployment as complete:

- [ ] Application accessible via production URL
- [ ] All features working correctly
- [ ] No console errors
- [ ] Performance metrics acceptable
- [ ] SSL certificate active
- [ ] Domain configured (if using custom domain)
- [ ] Monitoring set up
- [ ] Backup strategy in place
- [ ] Team notified of production URL
