# AdScorer

Standalone platform-specific ad copy grader. Built as a static-path sibling that gets served at **bilko.run/projects/ad-scorer/** by the [bilko-run host](https://github.com/StanislavBG/bilko-run).

Calls `bilko.run/api/demos/ad-scorer{,/compare,/generate}` same-origin — Clerk session cookie + JWT travel automatically.

## Build + sync

```bash
pnpm install
pnpm build              # emits dist/
pnpm sync               # copies dist/ to ../Bilko/public/projects/ad-scorer/
```

Or, from a Claude session in this repo, use the `bilko-host` MCP — it'll register the project, copy the build output, commit, and push to both remotes for you.

## Architecture

- React 18 + Vite 6 + Tailwind v4. No router. Bundles `@clerk/clerk-react` for SignInButton + JWT bearer auth.
- Slim local kit (`src/kit.tsx`) for `track()`, `<ToolHero>`, `<ScoreCard>`, `<SectionBreakdown>`, `<CompareLayout>`, `<Rewrites>`, `<CrossPromo>`. Host's full kit lives at `~/Projects/Bilko/src/components/tool-page/`.
- `useToolApi` (3 endpoints: submit / compare / generate) hooks the standalone to `bilko.run/api` same-origin. Server route stays in the host.
- Vite `base: /projects/ad-scorer/` so all assets resolve under that path.

## Modes

- **Score** — POST `/api/demos/ad-scorer` (1 credit) — paste an ad, get a score + 4-pillar breakdown + AI rewrites
- **A/B Compare** — POST `/api/demos/ad-scorer/compare` (2 credits) — paste two variants, get a winner with side-by-side breakdown
- **Generate** — POST `/api/demos/ad-scorer/generate` (1 credit) — describe a product, get 3 platform-optimized ads back

Each mode picks one of three platforms (Facebook / Google / LinkedIn) which adjusts character limits and scoring weights server-side.

## Files

- `src/AdScorerPage.tsx` — the page (extracted from `~/Projects/Bilko/src/pages/AdScorerPage.tsx`)
- `src/main.tsx` — mount point + ClerkProvider
- `src/index.css` — Tailwind + warm/fire/emerald/grade palette tokens + display utilities (`text-display-sm` is needed by `<CompareLayout>`)
- `src/kit.tsx` — slim `track()` + ToolHero/ScoreCard/SectionBreakdown/CompareLayout/Rewrites/CrossPromo
- `src/useToolApi.ts` — same hook as host, points to `https://bilko.run/api`
- `vite.config.ts` — base path + tailwind plugin
- `.mcp.json` — wires up `bilko-host` MCP for self-publish from a Claude session
