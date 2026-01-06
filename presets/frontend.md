# Frontend

Preset for web frontend/UI code. Auto-detects framework and patterns.

## Detection Signals

Identify framework from:
- React: `.jsx`/`.tsx` files, `react` in dependencies, hooks usage
- Vue: `.vue` files, `vue` in dependencies, composition API
- Svelte: `.svelte` files, `svelte` in dependencies
- Angular: `angular.json`, `@angular/*` dependencies
- Solid: `solid-js` in dependencies

Identify patterns from:
- State management: Redux, Zustand, Pinia, MobX, Jotai
- Styling: CSS modules, Tailwind, styled-components, Emotion
- Routing: React Router, Vue Router, SvelteKit
- Data fetching: React Query, SWR, Apollo Client

Identify build tools from:
- Vite: `vite.config.ts`, `vite.config.js`
- Webpack: `webpack.config.js`
- Parcel: `.parcelrc`
- esbuild: `esbuild.config.js`
- Turbopack: Next.js with turbo flag

Identify SSR/SSG frameworks:
- Next.js: `next.config.js`, `app/` or `pages/` directory
- Nuxt: `nuxt.config.ts`, `.nuxt/`
- SvelteKit: `svelte.config.js`, `src/routes/`
- Astro: `astro.config.mjs`
- Remix: `remix.config.js`

## Search Keywords

Based on detected framework:
- "[framework] best practices [year]"
- "[framework] component patterns"
- "[framework] performance optimization"
- "[framework] testing patterns"

Based on detected patterns:
- "[state-lib] patterns best practices"
- "[styling-approach] conventions"

General frontend:
- "frontend architecture patterns"
- "component design patterns"
- "accessibility best practices"

## Investigation Focus

When analyzing local frontend code, examine:
- **Component structure** — File organization, naming, composition patterns
- **State management** — Local vs global state, data flow patterns
- **Styling approach** — CSS organization, naming conventions, theming
- **Data fetching** — How is data loaded? Caching? Loading states?
- **Error boundaries** — How are errors handled in UI?
- **Accessibility** — ARIA usage, keyboard navigation, semantic HTML
- **Performance patterns** — Lazy loading, memoization, virtualization
- **Form handling** — Form libraries (React Hook Form, Formik, VeeValidate)
- **Internationalization** — i18n libraries, translation file structure
- **Design system** — Component library usage, tokens, theming architecture

## Policy Considerations

Common sections for frontend policies:
- Component naming and structure
- State management patterns
- Styling conventions
- Data fetching patterns
- Error handling in UI
- Accessibility requirements
- Performance guidelines
- Testing (unit, integration, e2e)

## CI/CD Detection

Look for pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml`
- Vercel: `vercel.json`
- Netlify: `netlify.toml`
- CloudFlare Pages: `wrangler.toml`

## Security Investigation

Examine security-related patterns:
- **CSP headers** — Content Security Policy configuration
- **XSS prevention** — Sanitization, escaping practices
- **Auth token handling** — Storage (cookies vs localStorage), refresh patterns
- **Dependency audit** — npm audit, Snyk configuration

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns**: `**/components/**/*`, `**/pages/**/*`, `**/app/**/*`, `**/*.tsx`, `**/*.vue`, `**/*.svelte`
