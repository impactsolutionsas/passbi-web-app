# Copilot Instructions for passbi-web-app

- Codebase: `next` (v15) + React 19 + TypeScript + Tailwind + Leaflet; app router under `src/app`
- Main domain: a transit trip planner, with route search, stops, line lists, and detailed journey pages.

## Quick start
- `npm install`
- `npm run dev` (default Next `@localhost:3000`)
- `npm run build` then `npm run start` for production preview
- `npm run lint` uses `next lint` (no dedicated test command present)
- Backend API: uses `NEXT_PUBLIC_API_URL` or fallback `http://localhost:8080` (`src/lib/constants.ts`)

## Architecture & data flow
- API client in `src/lib/api.ts`, always `GET` JSON with `cache: 'no-store'`.
- `src/app/search/page.tsx` stores recent searches in `localStorage` and navigates to `/results` with query params.
- `/src/app/results/page.tsx` reads search params and calls `api.routeSearch()`.
- Map components in `src/components/map/*` and wrappers (`MapWrapper`, `JourneyRouteMapWrapper`).
- Global app context in `src/app/providers.tsx`:
  - React Query (`@tanstack/react-query`)
  - i18n from `src/lib/i18n.tsx`
  - service worker registration (`/sw.js`)

## Project conventions
- path alias `@/` maps to `src/` (tsconfig + Next config)
- client components are marked with `'use client'`; some pages are client-only for hooks (`useRouter`, geolocation).
- data and interaction are typically via hooks + prop drilling; e.g. `useGeolocation`, `useNearbyStops`, `useI18n`.
- vectorized mode/style constants in `src/lib/constants.ts` (`MODE_COLORS`, `MODE_LABELS`, `AGENCY_LOGOS`).
- local browser-only features (geolocation, localStorage) are guarded with `typeof window !== 'undefined'`.

## Integration points to preserve
- `src/lib/types.ts` and `src/lib/journey-types.ts` for API shapes; keep in sync with backend v2 endpoints.
- `src/components/stop/DepartureBoard.tsx` and `src/components/journey/LegTimeline.tsx` render route legs and timeline details.
- `src/app/stop/[id]/page.tsx`, `src/app/lines/[id]/page.tsx`, `src/app/journey/page.tsx` rely on query params and dynamic routes.

## PR copy guidance for AI edits
- Minimize changes inside hardcoded UI gradients / static text; most behavior is data flow and route parameter handling.
- Any API URL changes should be done via env var, not inline.
- Add i18n text keys in `src/lib/i18n.tsx` and use `useI18n().t.*` in components.

> Ask for feedback on gaps: mention if you need details on expected UX transitions for `/results` ➜ `/journey`, or if you want guidance on composition of `MapWrapper` + stop click events.
