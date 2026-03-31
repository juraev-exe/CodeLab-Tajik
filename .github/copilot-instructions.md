# CodeLab Tajik — Workspace Guidelines

A browser-based coding practice platform with multilingual support (Tajik, Russian, English) for problem solving, interview prep, and progress tracking.

---

## Code Style

### TypeScript/JavaScript
- **Framework**: Next.js (App Router preferred for new features)
- **Type Safety**: All files in `src/` should use TypeScript (`.ts`, `.tsx`)
- **Formatting**: Run `prettier --write` on all files. Key settings:
  - 2-space indentation
  - Semicolons required
  - Single quotes for strings (except JSX attributes)
- **Components**: Functional components with hooks. Colocate tests and component files.
- **Imports**: Absolute imports via `@/` alias (configured in `tsconfig.json`)

### Code Organization
```
src/
├── app/               # Next.js App Router pages and layouts
├── components/        # Reusable React components
├── hooks/             # Custom React hooks
├── lib/               # Utilities and helpers
├── types/             # TypeScript type definitions
├── styles/            # Global and shared styles (Tailwind)
└── __tests__/         # Test files (colocated by feature)
```

---

## Architecture

### Core Services
1. **Frontend** (Next.js)
   - Server-side rendering for SEO
   - Client-side components for interactivity
   - PWA support for offline reading (service worker in `public/sw.js`)

2. **Backend** (Next.js API Routes in `src/app/api/`)
   - Authentication: Supabase Auth (email/phone OTP)
   - Database: Supabase PostgreSQL
   - Code Execution: Judge0 API wrapper (handles sandboxing)

3. **External Services**
   - **Judge0**: Stateless code execution (no in-memory scoring)
   - **Supabase**: Auth + DB + Real-time subscriptions
   - **Vercel/Railway**: Deployment platform

### Key Data Models
- **Users**: `id`, `email`, `username`, `language_preference` (tajik|russian|english), `created_at`
- **Problems**: `id`, `title`, `description`, `difficulty`, `language_support`, `test_cases`
- **Submissions**: `user_id`, `problem_id`, `code`, `language`, `status`, `verdict`, `timestamp`
- **Progress**: `user_id`, `solved_count`, `streak`, `last_solved_date`

---

## Build and Test

### Prerequisites
```bash
# Node.js 18+ and npm/yarn/pnpm
# PostgreSQL (or use Supabase CLI for local dev)
# Judge0 API key (for code execution)
```

### Development Setup
```bash
# Install dependencies
npm install

# Copy environment template
cp .env.example .env.local

# Start dev server
npm run dev       # http://localhost:3000

# Run tests
npm test

# Type checking
npm run type-check

# Linting
npm run lint
```

### Key Scripts
- `npm run build` — Production build for Vercel
- `npm run dev` — Local dev server with hot reload
- `npm run test` — Jest/Vitest (verify this matches your setup)
- `npm run lint` — ESLint + TypeScript checks
- `npm run type-check` — TypeScript compilation check only

### Testing Strategy
- **Unit tests**: `__tests__` or `.test.ts` files
- **Integration tests**: API route testing with Supabase test database
- **E2E tests**: Playwright or Cypress (if configured)
- **Coverage target**: 70% minimum for critical paths (auth, scoring)

---

## Conventions

### Multilingual Support
- **I18n**: Use `next-intl` or similar for Tajik/Russian/English
- **Language Codes**: `tj` (Tajik), `ru` (Russian), `en` (English)
- **Problem Descriptions**: Store in single `description` field with language-specific content, or use i18n keys
- **Testing**: Always test with at least 2 languages

### API Routes
- Prefix protected routes with `/api/protected/` for clarity
- Use Supabase middleware for auth checks
- Return consistent JSON: `{ success, data/error, message }`
- HTTP status codes: 200 (ok), 201 (created), 400 (bad request), 401 (auth), 404 (not found), 500 (server error)

### Database Migrations
- Use Supabase migration tool: `supabase migration new <name>`
- Store migrations in `supabase/migrations/`
- Always test migrations locally first

### Judge0 Integration
- Wrapper in `src/lib/judge0.ts` handles API calls, polling, and error handling
- All code submissions go through this wrapper
- Handle timeout (Judge0 can take 5-30s depending on queue)

### Environment Variables
- **Required**: `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_KEY`, `JUDGE0_API_KEY`
- **Optional**: `NEXT_PUBLIC_JUDGE0_URL` (default: api.judge0.com)
- Add new vars to `.env.example` and document them

---

## Common Development Patterns

### Creating a New Problem
1. Add problem to database via Supabase dashboard or migration
2. Create problem details page in `src/app/problems/[id]/page.tsx`
3. Use shared components for code editor and submission form
4. Test submission flow end-to-end

### Adding a Feature
1. Branch from `main`: `git checkout -b feature/my-feature`
2. Implement with types first (TDD mindset)
3. Run linting and tests locally: `npm run lint && npm test`
4. Open PR with description of what and why

### Debugging Judge0 Submissions
- Check Judge0 API response in browser DevTools or server logs
- Common issues: timeout (increase polling), memory limit exceeded, syntax error
- Log submission ID and status for troubleshooting

---

## Git Workflow

- **Main branch**: Always deployable, no breaking changes
- **Feature branches**: `feature/user-profiles`, `fix/leak-in-judge0-polling`
- **Commit messages**: Imperative, ~50 chars: "Add Tajik language support", not "Added support"
- **PRs**: Require tests and type-check passing before merge

---

## Where to Ask Questions

- **Architecture decisions**: See `ARCHITECTURE.md` (if present)
- **Database schema**: Check `supabase/migrations/` for latest schema
- **Testing examples**: Look at `src/__tests__/` for patterns
- **Deployment**: `DEPLOYMENT.md` or README setup section
