---
type: brainstem
tier: S
domain: coding
source_url: https://www.typescriptlang.org/docs/
last_updated: 2024-11-26
tags: [typescript, javascript, types, compiler, configuration, tsconfig]
related_projects: []
---

# TypeScript Documentation

## Overview

TypeScript is a strongly typed programming language that builds on JavaScript, adding static type definitions. Developed by Microsoft, it compiles to plain JavaScript and enables type safety, better tooling, and improved developer experience for large-scale applications.

## Key Concepts

- **Type System**: Static typing with type inference, interfaces, generics, unions, intersections
- **Compiler (tsc)**: Transpiles TypeScript to JavaScript, supports various module systems and targets
- **Configuration (tsconfig.json)**: Controls compiler behavior, module resolution, target output
- **Type Definitions (.d.ts)**: Declaration files for JavaScript libraries, enabling type checking
- **Module Systems**: Supports CommonJS, ES Modules, AMD, SystemJS, and bundler-specific resolution

## When to Use

- **Large codebases**: Type safety catches errors at compile time
- **Team collaboration**: Types serve as documentation and contracts
- **Refactoring**: Types enable safe, automated refactoring
- **IDE support**: Autocomplete, navigation, and error detection improve productivity
- **Library development**: Type definitions improve API discoverability

## Failure Modes

- **Over-typing**: Excessive type annotations reduce readability; prefer inference
- **Any abuse**: Using `any` defeats type safety; use `unknown` or proper types
- **Config complexity**: Overly complex tsconfig.json; start simple, add as needed
- **Module resolution issues**: Mismatched module/moduleResolution settings cause import errors
- **Performance**: Large projects may have slow type checking; use project references

## Common Patterns

- **Strict mode**: Enable `strict: true` for maximum type safety
- **Project references**: Split monorepos into separate TypeScript projects
- **Declaration files**: Create `.d.ts` files for untyped JavaScript libraries
- **Utility types**: Use `Partial`, `Pick`, `Omit`, `Record` for type transformations

## Related Sources

- [[MDN_Web_Docs]] - JavaScript reference
- [[React_Docs]] - React with TypeScript patterns

