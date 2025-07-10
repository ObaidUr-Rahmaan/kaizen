# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Kaizen is a modern, production-ready SaaS starter template built on React Router v7. It features a modular, feature-flag-based architecture where functionality can be enabled/disabled through configuration.

## Key Technologies

- **React Router v7** - Full-stack React framework with SSR
- **React 19** with TypeScript
- **Convex** - Real-time serverless backend (optional)
- **Clerk** - Authentication (optional)
- **Polar.sh** - Subscription billing (optional)
- **TailwindCSS v4** with shadcn/ui components
- **Vite** - Build tooling

## Essential Commands

### Development
```bash
npm run dev              # Start development server
npx convex dev          # Start Convex backend (if enabled in config.ts)
```

### Building & Type Checking
```bash
npm run build           # Production build
npm run typecheck       # TypeScript checking (runs react-router typegen && tsc)
```

### Testing
```bash
npm run test            # Run unit tests with Vitest
npm run test:watch      # Watch mode for unit tests
npm run test:e2e        # Run Playwright E2E tests
npm run test:all        # Run all tests (unit + E2E)
```

### Single Test Execution
```bash
npm run test -- path/to/test.test.ts          # Run specific unit test
npm run test:e2e -- path/to/test.spec.ts      # Run specific E2E test
```

## Architecture & Structure

### Feature-Flag System
The entire application is configured through `config.ts`. Features must be enabled there before use:
- `auth` - Clerk authentication
- `payments` - Polar.sh billing
- `convex` - Backend database
- `email` - Transactional emails
- `monitoring` - Error tracking

### Directory Structure
```
/app/
  /components/       # Reusable UI components
    /ui/            # shadcn/ui base components
  /routes/          # React Router route handlers
    /dashboard/     # Protected dashboard routes
  /hooks/           # Custom React hooks
  /lib/             # Utilities and helpers
  root.tsx          # App root with providers
  routes.ts         # Dynamic route configuration

/convex/            # Backend functions (if enabled)
  schema.ts         # Database schema
  _generated/       # Auto-generated Convex code
```

### Key Integration Points

1. **Route Configuration** - `app/routes.ts` dynamically includes routes based on enabled features
2. **Provider Setup** - `app/root.tsx` conditionally wraps the app with providers (Clerk, Convex) based on config
3. **Service Initialization** - Services are lazy-loaded only when their features are enabled
4. **Type Safety** - All services have TypeScript interfaces; use feature checks before accessing

### Development Workflow

1. **Configure First**: Always start by setting features in `config.ts`
2. **Environment Variables**: Copy `.env.example` to `.env.local` and fill required values for enabled features
3. **Start Services**: Run `npm run dev` and `npx convex dev` (if using Convex)
4. **Feature Guards**: Always check if a feature is enabled before using it:
   ```typescript
   import { isFeatureEnabled } from '~/config';
   
   if (isFeatureEnabled('auth')) {
     // Use auth features
   }
   ```

### Testing Approach

- **Unit Tests**: Located alongside components or in `test/` directory
- **E2E Tests**: Located in `e2e/` directory
- **Test Utilities**: Available in `test/setup.ts` and `test/utils.tsx`
- Tests use Vitest with React Testing Library for components

### Common Patterns

1. **Conditional Imports**: Services are imported conditionally to reduce bundle size
2. **Feature Checks**: Use `isFeatureEnabled()` and `isServiceEnabled()` helpers
3. **Type Guards**: TypeScript will enforce feature checks when accessing optional services
4. **Route Protection**: Dashboard routes automatically require auth when enabled

### Deployment

- Primary deployment target is Vercel (optimized with `@vercel/react-router` preset)
- Environment variables must be set in deployment platform
- Convex requires separate deployment via CLI: `npx convex deploy`