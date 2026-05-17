# JEE AI Study Assistant

An AI-powered study companion for JEE (Joint Entrance Examination) preparation, featuring quizzes, score tracking, weak topic analysis, a daily revision planner, and an AI tutor chat.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 8080)
- `pnpm --filter @workspace/jee-assistant run dev` — run the frontend (port 23694)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string
- Required env: `AI_INTEGRATIONS_OPENAI_BASE_URL`, `AI_INTEGRATIONS_OPENAI_API_KEY` — Replit AI integration (auto-provisioned)

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind CSS + shadcn/ui (dark theme)
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- AI: Replit AI Integrations (OpenAI-compatible, gpt-5-mini)
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — OpenAPI spec (source of truth)
- `lib/db/src/schema/jee.ts` — JEE app DB schema (subjects, topics, questions, quiz sessions, planner)
- `lib/db/src/schema/conversations.ts`, `messages.ts` — AI chat conversation schema
- `artifacts/api-server/src/routes/` — Express route handlers
- `artifacts/jee-assistant/src/` — React frontend

## Architecture decisions

- Dark mode is permanently applied via `class="dark"` on `<html>` — no theme toggle needed
- AI question generation uses gpt-5-mini for cost-efficiency with `response_format: json_object`
- Quiz sessions store question IDs in a Postgres array for simplicity
- Weak topic analysis is computed on-the-fly from quiz answer data (no materialized view needed at this scale)
- All API contracts are defined in OpenAPI first; frontend uses only Orval-generated hooks

## Product

- **Dashboard** — study streak, avg score, today's study time, subject mastery bars, score history chart
- **Quiz Arena** — pick subject/topic/difficulty, answer MCQs, get instant feedback with explanations, see score card
- **Planner** — weekly revision calendar, add/complete/delete tasks, AI-generate a week plan
- **Analytics** — weak topic ranking by error rate, subject score breakdown, score history chart
- **Subjects** — browse Physics/Chemistry/Maths syllabus with topics
- **AI Chat** — ask any JEE concept to the AI tutor

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- After any OpenAPI spec change, always run `pnpm --filter @workspace/api-spec run codegen` before touching frontend or backend code
- The `GenerateQuestionsBody` Zod schema comes from codegen — use that name (not manual inference)
- `options` column in `questions` table is a Postgres text array
- Orval names: body schemas are `XxxBody`, query params are `XxxQueryParams`, path params are `XxxParams`
