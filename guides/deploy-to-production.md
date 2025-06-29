# Deploying to Production Guide

This guide provides instructions for deploying the application to production using Vercel and Convex.

**Date:** June 2025

## Prerequisites

- A Git repository with your code (GitHub, GitLab, or Bitbucket)
- A [Vercel](https://vercel.com) account
- Production accounts for:
  - [Convex](https://dashboard.convex.dev)
  - [Clerk](https://dashboard.clerk.com)
  - [Polar](https://polar.sh) (not sandbox)
  - [Resend](https://resend.com)
  - [OpenAI](https://platform.openai.com)

## Step 1: Prepare Your Repository

1. Ensure all your changes are committed
2. Push your code to your Git repository
3. Make sure your `.env` and `.env.local` files are in `.gitignore`

## Step 2: Deploy Backend to Convex

1. Deploy your backend to Convex:
```bash
npx convex deploy
```

2. Note your production deployment URL: `https://your-project-name.convex.cloud`

## Step 3: Configure Production Services

### Clerk Setup
1. Create a new production application in Clerk (or switch to production mode)
2. Get your production API keys
3. Set up the JWT template for Convex:
   - Go to JWT Templates
   - Create a new Convex template
   - Save the issuer URL

### Polar Setup
1. Switch to [polar.sh](https://polar.sh) (not sandbox)
2. Create your production organization
3. Set up your production plans
4. Get your production API keys and organization ID

### Resend Setup
1. Verify your production domain
2. Create a production API key
3. Generate a webhook signing secret

## Step 4: Set Convex Environment Variables

In the [Convex Dashboard](https://dashboard.convex.dev), add these environment variables:

```env
# Company & Email Configuration
DEFAULT_FROM_EMAIL=support@yourdomain.com
COMPANY_NAME="Your Company Name"

# Frontend Configuration
FRONTEND_URL="https://your-vercel-domain.com"

# OpenAI
OPENAI_API_KEY="sk-..."

# Clerk
VITE_CLERK_FRONTEND_API_URL="https://your-clerk-frontend-api-url"

# Polar
POLAR_ACCESS_TOKEN="pk_live_..."
POLAR_ORGANIZATION_ID="org_..."
POLAR_WEBHOOK_SECRET="whsec_..."

# Resend
RESEND_API_KEY="re_..."
```

## Step 5: Deploy to Vercel

1. Go to [Vercel](https://vercel.com)
2. Create a new project
3. Import your Git repository
4. Configure the build settings:
   - Framework Preset: Vite
   - Build Command: `npm run build`
   - Output Directory: `dist`
   - Install Command: `npm install --legacy-peer-deps`

5. Add these environment variables in Vercel:
```env
# Convex (client-side URLs)
VITE_CONVEX_URL="https://your-deployment.convex.cloud"

# Clerk
VITE_CLERK_PUBLISHABLE_KEY="pk_live_..."  # For client-side usage
CLERK_SECRET_KEY="sk_live_..."            # Required for build time
```

Note: All other environment variables (FRONTEND_URL, email configuration, etc.) should be set in your Convex deployment, not in Vercel.

6. Deploy the project

## Step 6: Configure Webhooks

### Polar Webhooks
1. In Polar dashboard:
   - Go to Webhooks
   - Add endpoint: `https://your-convex-deployment.convex.cloud/payments/webhook`
   - Format: Raw
   - Select all event types
   - Save the webhook

### Resend Webhooks
1. In Resend dashboard:
   - Go to Webhooks
   - Add endpoint: `https://your-convex-deployment.convex.cloud/resend-webhook`
   - Save the webhook

## Step 7: Update Service Configurations

### Clerk
1. In Clerk Dashboard:
   - Add your Vercel domain to allowed origins
   - Update OAuth callback URLs
   - Update any email templates with production URLs

### Polar
1. Update subscription success/cancel URLs to your production domain
2. Test a subscription with a real card

### Resend
1. Set up domain authentication for better deliverability
2. Create production email templates

## Step 8: Final Testing

1. Test the complete user flow:
   - Sign up
   - Email verification
   - Subscription process
   - Dashboard access
   - Settings page
   - Subscription management

2. Monitor logs in:
   - Convex Dashboard
   - Clerk Dashboard
   - Polar Dashboard
   - Resend Dashboard

## Production Checklist

- [ ] All environment variables are set in Convex
- [ ] All environment variables are set in Vercel
- [ ] Clerk JWT template is configured
- [ ] Polar webhooks are pointing to production URL
- [ ] Resend webhooks are pointing to production URL
- [ ] Domain verification is complete in Resend
- [ ] All OAuth callbacks are updated in Clerk
- [ ] Subscription plans are set up in Polar
- [ ] OpenAI API key has sufficient quota
- [ ] Error monitoring is set up in Convex

## Troubleshooting

- If webhooks aren't working, verify the endpoint URLs and secrets
- If emails aren't sending, check Resend domain verification
- If auth isn't working, verify Clerk origins and JWT configuration
- If subscriptions fail, check Polar webhook configuration
- Monitor Convex logs for any backend errors 