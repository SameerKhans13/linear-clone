# Linear Clone - AI Coding Agent Instructions

## Project Overview

This is a **Linear.app clone** built as a fullstack project management application with real-time collaboration. The project is in early development, bootstrapped from a Turborepo template with ambitious plans detailed in `AGENTS.md`.

**Current State**: Basic Turborepo monorepo with two Next.js apps (`web`, `docs`) and shared packages (`ui`, `eslint-config`, `typescript-config`). Backend (Hono.js), database (PostgreSQL + Drizzle), and most features from `AGENTS.md` are **not yet implemented**.

## Architecture

### Monorepo Structure (Turborepo)

```
linear-clone/
├── apps/
│   ├── web/        # Main Next.js app (port 3000) - will be the primary frontend
│   └── docs/       # Next.js docs app (port 3001) - purpose TBD, may be removed
├── packages/
│   ├── ui/         # Shared React components (@repo/ui)
│   ├── eslint-config/     # Shared ESLint configs
│   └── typescript-config/ # Shared TypeScript configs
```

**Planned additions** (from `AGENTS.md`):

- `apps/api/` - Hono.js backend with WebSocket support
- `packages/database/` - Drizzle ORM schemas and migrations
- `packages/types/` - Shared TypeScript types

### Technology Stack

**Current**:

- Package Manager: npm (v11.6.2, specified in `package.json`)
- Build System: Turborepo 2.6.0
- Frontend: Next.js 16 with React 19, App Router
- TypeScript: 5.9.2 (strict mode expected)
- Code Quality: ESLint 9 with Prettier integration

**Planned** (per `AGENTS.md`):

- Backend: Hono.js with WebSockets
- Database: PostgreSQL with Drizzle ORM
- Auth: Better Auth
- Testing: Vitest
- Styling: Tailwind CSS (not yet configured)
- UI: Radix UI primitives
- State: Zustand

## Development Workflows

### Running the Project

```bash
# Install dependencies (always use npm)
npm install

# Start all apps in development mode
npm run dev

# Start specific app (use filters)
npx turbo dev --filter=web
npx turbo dev --filter=docs

# Build all apps
npm run build

# Lint all code
npm run lint

# Format all code
npm run format

# Type check all apps
npm run check-types
```

### Turborepo Commands

- **Always use `npx turbo` or `npm run`** - Global turbo is not required
- **Use filters** for specific packages: `--filter=web`, `--filter=@repo/ui`
- **Cache behavior**: Turbo caches builds automatically (see `turbo.json`)

### Package Management

- **Internal dependencies**: Reference as `"@repo/ui": "*"` in `package.json`
- **Adding dependencies**: Run `npm install` in the specific workspace folder (`apps/web`, `packages/ui`, etc.)
- **Version consistency**: Keep React, TypeScript versions aligned across packages

## Code Conventions

### TypeScript

- **Strict mode**: Enable in all `tsconfig.json` files
- **No `any`**: Use proper types or `unknown`
- **Import patterns**:
  - Internal packages: `import { Button } from "@repo/ui/button"`
  - Next.js: Use `type` for type imports: `import type { Metadata } from "next"`

### React/Next.js

- **App Router only**: All routes in `app/` directory
- **Server Components by default**: Use `"use client"` explicitly when needed
- **Font loading**: Use `next/font/local` for custom fonts (see `apps/web/app/layout.tsx`)
- **Component naming**: PascalCase for components, lowercase for files (e.g., `button.tsx` exports `Button`)

### Shared UI Components (`packages/ui`)

- **Export pattern**: Individual file exports via `"exports": {"./*": "./src/*.tsx"}`
- **Usage**: Import as `@repo/ui/button`, not `@repo/ui`
- **Props**: Always include explicit TypeScript interfaces
- **Client components**: Mark as `"use client"` when using hooks/events

### ESLint Configuration

- **Base config**: `@repo/eslint-config/base` for general TypeScript
- **Next.js apps**: Use `@repo/eslint-config/next-js` (includes React, React Hooks, Next.js rules)
- **React internal**: Use `@repo/eslint-config/react-internal` for shared UI packages
- **Prettier integration**: Already configured, no conflicts

### File Naming

- **Components**: lowercase kebab-case files (`button.tsx`, `issue-card.tsx`)
- **Routes**: App Router conventions (`page.tsx`, `layout.tsx`, `route.ts`)
- **Types**: Define in same file or dedicated `types.ts`

## Critical Patterns from Codebase

### Turborepo Task Pipeline

From `turbo.json`:

- **Build tasks** depend on upstream builds: `"dependsOn": ["^build"]`
- **Dev tasks** are persistent and uncached: `"cache": false, "persistent": true`
- **Environment variables**: `.env*` files are included in build inputs
- **Outputs**: Next.js builds output to `.next/**` (excluding cache)

### Next.js Configuration

- **Minimal config**: Default `next.config.js` is nearly empty - configure as needed
- **Image optimization**: Use `next/image` (already used in `apps/web/app/page.tsx`)
- **TypeScript plugin**: Enabled via `tsconfig.json` for Next.js autocomplete

### Monorepo Dependencies

- **Workspace references**: Use `"workspaces": ["apps/*", "packages/*"]` in root
- **Internal packages**: Always use `"*"` version for monorepo packages
- **TypeScript references**: Extended from `@repo/typescript-config` packages

## Integration Points

### Frontend ↔ Backend (Planned)

From `AGENTS.md`, the API will be:

- **Base URL**: `http://localhost:3001/api` (Hono.js backend)
- **Authentication**: Better Auth with JWT tokens
- **WebSocket**: Room-based pub/sub for real-time updates
- **CORS**: Configure to allow `http://localhost:3000` (web app)

### Database Access (Planned)

- **ORM**: Drizzle with PostgreSQL driver
- **Migrations**: Run via `packages/database` scripts
- **Schema organization**: Separate files per entity (`users.ts`, `issues.ts`, etc.)

### Real-time Synchronization (Planned)

- **Protocol**: WebSockets (ws library)
- **Pattern**: Client subscribes to workspace/team rooms
- **Events**: Issue updates, comments, status changes
- **State sync**: Optimistic updates in Zustand stores with WebSocket reconciliation

## Important Context

### What Exists vs. What's Planned

**Currently implemented**:

- ✅ Turborepo monorepo structure
- ✅ Two Next.js apps with basic scaffolding
- ✅ Shared UI package with sample button
- ✅ ESLint/TypeScript configs
- ✅ Build/dev/lint pipelines

**From AGENTS.md but NOT implemented**:

- ❌ Backend API (Hono.js)
- ❌ Database and ORM
- ❌ Authentication
- ❌ Issue tracking features
- ❌ Project management features
- ❌ Real-time collaboration
- ❌ Tailwind CSS
- ❌ Testing setup
- ❌ Any Linear-like UI/UX

### Design Philosophy (from AGENTS.md)

When implementing new features:

- **Linear-inspired UX**: Smooth animations (150-300ms), keyboard shortcuts, command palette
- **Performance first**: Optimistic updates, virtualized lists, proper memoization
- **Type safety**: Full TypeScript coverage, Zod for runtime validation
- **Real-time by default**: All mutations should broadcast via WebSocket
- **Accessibility**: Use Radix UI primitives for proper ARIA support

### Keyboard Shortcuts (Planned)

- `⌘K`/`Ctrl+K`: Command palette
- `C`: Create issue
- `/`: Focus search
- `?`: Show shortcuts help
- `G then I`: Go to issues
- `Esc`: Close modals

## Common Tasks

### Adding a New Shared UI Component

```bash
cd packages/ui
# Create component file
# Export via individual file export (already configured)
# Import in apps as: import { ComponentName } from "@repo/ui/component-name"
```

### Creating a New App

```bash
# Add to apps/ directory
# Update workspaces in root package.json (already includes apps/*)
# Configure in turbo.json if custom tasks needed
```

### Adding Database Schema (When Ready)

Per `AGENTS.md`:

1. Create schema file in `packages/database/schema/`
2. Use Drizzle schema builder
3. Generate migration: `npm run db:generate`
4. Apply migration: `npm run db:migrate`

### Setting Up Environment Variables

Create `.env` files in each app (not committed):

- `apps/web/.env.local` for Next.js
- `apps/api/.env` for backend
- Turbo includes `.env*` in build inputs automatically

## Questions to Clarify

When implementing features, consider:

1. **Is the `docs` app needed?** It's a standard Turborepo template artifact but unclear if it fits Linear clone vision.

2. **Biome.js vs ESLint/Prettier**: `AGENTS.md` specifies Biome.js for linting/formatting, but project currently uses ESLint + Prettier. Which to use?

3. **Database setup**: Should Drizzle be added before implementing backend, or can backend routes be stubbed first?

4. **Authentication flow**: Should Better Auth integration happen before or alongside first protected routes?

5. **Testing strategy**: Vitest mentioned in `AGENTS.md` but no tests exist. Start writing tests now or after core features?

---

**Key Reference**: See `AGENTS.md` for comprehensive feature requirements, database schema design, and phase-by-phase implementation plan. Treat it as the source of truth for what to build, while this file describes how the existing codebase works.
