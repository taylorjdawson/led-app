# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Package Manager

This project uses **npm** as the package manager with npm workspaces for monorepo management.

## Common Commands

### Development
- `npm install` - Install all dependencies
- `npm run dev` - Start development server (uses Turbo)
- `npm run build` - Build all packages (uses Turbo)
- `npm run lint` - Run linting across all packages (uses Turbo)

### Working with specific apps
- `npm run dev --workspace=web` - Start only the web app
- `npm run build --workspace=web` - Build only the web app
- `npm run lint --workspace=web` - Lint only the web app
- `npm run typecheck --workspace=web` - Type check the web app

### Adding shadcn/ui components
- `npx shadcn@latest add button -c apps/web` - Add a component to the web app
- Components are placed in `packages/ui/src/components/`

## Architecture

This is a **monorepo** using:
- **Turbo** for build orchestration and caching
- **npm workspaces** for dependency management
- **shadcn/ui** for component library structure

### Project Structure
```
apps/
  web/                    # Next.js application
    app/                  # Next.js app router
    components/           # App-specific components
    hooks/               # Custom React hooks
    lib/                # App utilities

packages/
  ui/                   # Shared UI component library
    src/components/     # shadcn/ui components
    src/hooks/         # Shared hooks
    src/lib/           # Shared utilities
    src/styles/        # Global styles
  eslint-config/        # Shared ESLint configuration
  typescript-config/    # Shared TypeScript configuration
```

### Key Dependencies
- **Next.js 15** with App Router and Turbopack
- **React 19**
- **shadcn/ui** component system
- **Tailwind CSS** for styling
- **next-themes** for theme management
- **lucide-react** for icons

### Component Usage
Import UI components from the workspace package:
```tsx
import { Button } from "@workspace/ui/components/button"
```

### Workspace Dependencies
The monorepo uses npm workspaces for internal dependencies. Package names:
- `@workspace/ui` - UI component library
- `@workspace/eslint-config` - ESLint configuration
- `@workspace/typescript-config` - TypeScript configuration

## Development Notes

- The web app uses Next.js with Turbopack for faster development
- All packages share common ESLint and TypeScript configurations
- Turbo handles task orchestration and caching across packages
- UI components follow shadcn/ui conventions and are shared across applications