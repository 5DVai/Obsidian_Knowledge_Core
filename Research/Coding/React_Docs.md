---
type: brainstem
tier: S
domain: coding
source_url: https://react.dev/
last_updated: 2024-11-26
tags: [react, javascript, ui-library, components, hooks, jsx]
related_projects: []
---

# React Documentation

## Overview

React is a JavaScript library for building user interfaces, particularly web and native applications. Developed by Meta (formerly Facebook), React enables developers to build UIs from reusable components, manage state declaratively, and create interactive applications efficiently.

## Key Concepts

- **Components**: Reusable, composable UI pieces written as JavaScript functions
- **JSX**: JavaScript syntax extension for writing component markup inline
- **Hooks**: Functions that let components use state and other React features (useState, useEffect, useContext, etc.)
- **Props**: Data passed from parent to child components
- **State**: Component memory that triggers re-renders when updated
- **Virtual DOM**: React's efficient diffing algorithm for UI updates

## Architecture Patterns

- **Component-Based**: Build UIs from small, reusable pieces
- **Declarative**: Describe what the UI should look like, React handles updates
- **Unidirectional Data Flow**: Data flows down via props, events flow up
- **Server Components**: React 18+ feature for server-side rendering and data fetching
- **Frameworks**: Works with Next.js, Remix, React Router for full-stack apps

## When to Use

- Building interactive web applications
- Creating reusable UI component libraries
- Applications requiring frequent UI updates
- Projects needing strong developer tooling (React DevTools)
- When working with React Native for mobile apps
- Applications that benefit from component composition

## Failure Modes

- Over-engineering simple static pages (use plain HTML/CSS)
- Not understanding when to use effects vs. derived state
- Performance issues from unnecessary re-renders (though React Compiler helps)
- Learning curve for JSX and component patterns
- Requires build tooling (though can be added incrementally)

## Notable Features

- **React 19.2**: Latest stable version with compiler optimizations
- **React Compiler**: Automatically optimizes re-renders
- **Server Components**: Fetch data on server, stream to client
- **Concurrent Features**: Automatic batching, transitions, Suspense
- **React Native**: Same patterns for iOS/Android apps
- **Large Ecosystem**: Extensive third-party library support

## Related Sources

- [[MDN_Web_Docs]] - Web standards foundation
- [[Next.js_Docs]] - Full-stack React framework
- [[TypeScript_Docs]] - Type safety for React

## Update 2024-11-26

Initial distillation from React.dev homepage and search results. Core concepts and patterns extracted for RAG retrieval.

