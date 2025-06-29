# Testing Locally Guide

This guide will walk you through setting up the project for local development and testing.

**Date:** June 2025

## Prerequisites

- [Node.js](https://nodejs.org/) (v18 or later)
- [npm](https://www.npmjs.com/)
- [ngrok](https://ngrok.com/) or similar tunnel service (for testing webhooks)

## Step 1: Installation

1. Clone the repository and install dependencies:
```bash
git clone <repository-url>
cd <repository-name>
npm install --legacy-peer-deps --force
```

Note: We use `--legacy-peer-deps` because some packages might have React 18 peer dependencies while we're using React 19.

## Step 2: Set Up Convex

1. Open a new terminal and run:
```bash
npx convex dev
```

2. If not logged in, follow the prompts to log in
3. Create a new project when prompted
4. This will automatically add your Convex URL to `.env.local`

## Step 3: Set Up Clerk

1. Go to [Clerk Dashboard](https://dashboard.clerk.dev/)
2. Create a new application
3. Select "React Router" as your framework
4. Enable Email/Password and Google authentication methods
5. Go to API Keys and copy them to your `.env.local`:
```env
VITE_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...
```

## Step 4: Configure Clerk-Convex Integration

1. In Clerk Dashboard:
   - Go to JWT Templates
   - Create a new template
   - Select "Convex"
   - Save the template

2. Copy the Issuer URL from Clerk

3. In Convex Dashboard:
   - Go to Settings > Environment Variables
   - Add `VITE_CLERK_FRONTEND_API_URL` and set it to the Issuer URL from Clerk

## Step 5: Set Up Polar (Payments)

1. Go to [sandbox.polar.sh](https://sandbox.polar.sh)
2. Create a new organization
3. Create a subscription plan
4. Get your API keys from Settings

5. In Convex Dashboard, add these environment variables:
```env
POLAR_ACCESS_TOKEN=<your-access-token>
POLAR_ORGANIZATION_ID=<your-org-id>
POLAR_WEBHOOK_SECRET=<your-webhook-secret>
```

## Step 6: Configure Webhooks

1. Get your Convex HTTP URL from the Convex dashboard
2. In Polar Dashboard:
   - Go to Webhooks
   - Add a new endpoint: `<your-convex-http-url>/payments/webhook`
   - Select "Format as raw"
   - Generate a new secret
   - Select all event types
   - Click Create

## Step 7: Set Up Tunnel for Local Testing

Since Polar needs to reach your local environment for webhooks, set up a tunnel:

1. Start your tunnel:
```bash
ngrok http 5173
```

2. Copy the HTTPS URL provided by ngrok

3. Add it to your Convex environment variables:
```env
FRONTEND_URL=<your-ngrok-url>
```

4. Update `vite.config.ts` to allow the ngrok domain:
```typescript
server: {
  host: true,
  allowedHosts: ['your-ngrok-subdomain.ngrok.io']
}
```

## Step 8: Start the Development Server

1. In one terminal, keep Convex running:
```bash
npx convex dev
```

2. In another terminal, start the frontend:
```bash
npm run dev
```

Your app should now be running at `http://localhost:5173` and accessible via your ngrok URL for webhook testing.

## Environment Variables Summary

Here's a complete list of environment variables needed:

```env
# Company & Email Configuration
COMPANY_NAME="Your Company Name"
DEFAULT_FROM_EMAIL="noreply@yourdomain.com"

# Frontend Configuration
FRONTEND_URL="http://localhost:5173"

# OpenAI
OPENAI_API_KEY="sk-..."

# Clerk
VITE_CLERK_PUBLISHABLE_KEY="pk_test_..."
CLERK_SECRET_KEY="sk_test_..."

# Convex
CONVEX_DEPLOYMENT="..."
CONVEX_URL="..."

# Polar
POLAR_ACCESS_TOKEN="..."
POLAR_ORGANIZATION_ID="..."
POLAR_WEBHOOK_SECRET="..."

# Resend
RESEND_API_KEY="re_..."
```

## Testing the Setup

1. Visit your local URL
2. Try logging in with Clerk
3. Test the subscription flow with Polar's test card:
   - Card number: 4242 4242 4242 4242
   - Any future expiry date
   - Any CVC
   - Any billing information

## Troubleshooting

- If you see "No address provided to convex react client", check your environment variables
- If Polar webhooks aren't working, ensure your ngrok URL is correct and the webhook is properly configured
- If you get React version conflicts, make sure you used `--legacy-peer-deps` during installation 