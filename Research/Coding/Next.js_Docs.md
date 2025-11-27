---
type: brainstem
tier: S
domain: coding
source_url: https://nextjs.org/docs
last_updated: 2024-11-26
tags: [nextjs, react, framework, ssr, ssg, routing, data-fetching]
related_projects: []
---

# Next.js Documentation

## Overview

Next.js is a React framework for production that provides server-side rendering (SSR), static site generation (SSG), API routes, file-based routing, and optimized performance out of the box. Developed by Vercel, it simplifies building full-stack React applications.

## Key Concepts

- **App Router**: Modern routing system (Next.js 13+) using `app/` directory with layouts, loading states, error boundaries
- **Pages Router**: Traditional routing system using `pages/` directory (still supported)
- **Server Components**: React components that render on the server, reducing client bundle size
- **Data Fetching**: `fetch` API with automatic caching, revalidation, and streaming support
- **API Routes**: Serverless functions for backend logic, accessible at `/api/*` endpoints
- **Static Generation**: Pre-render pages at build time for optimal performance
- **Incremental Static Regeneration (ISR)**: Update static pages without full rebuilds

## When to Use

- **SEO-critical apps**: Server-side rendering improves search engine visibility
- **Performance**: Automatic code splitting, image optimization, font optimization
- **Full-stack apps**: Built-in API routes eliminate need for separate backend
- **React applications**: Leverages React ecosystem with enhanced developer experience
- **Production deployments**: Optimized for Vercel but works with any Node.js hosting

## Failure Modes

- **Client/Server boundary**: Accidentally using browser APIs in Server Components
- **Caching issues**: Misunderstanding `fetch` caching behavior leads to stale data
- **Route conflicts**: App Router and Pages Router cannot coexist in same routes
- **Build errors**: Large applications may hit memory limits during static generation
- **API route timeouts**: Serverless functions have execution time limits

## Common Patterns

- **Layouts**: Shared UI using `layout.tsx` files
- **Loading states**: `loading.tsx` for Suspense boundaries
- **Error handling**: `error.tsx` for error boundaries
- **Metadata**: SEO using `metadata` export or `generateMetadata` function
- **Middleware**: Request interception and modification using `middleware.ts`

## Related Sources

- [[React_Docs]] - React fundamentals
- [[TypeScript_Docs]] - TypeScript with Next.js

