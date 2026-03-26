# Technisch / kwaliteit

*SEO, metadata, crawlers, prestaties, veiligheid, consent — wat bezoekers meestal niet als “feature” zien.*

- **Metadata & SEO**
  - `metadataBase` + productie-URL uit env
  - Titel-template; canonical; bij meertalig: hreflang / `x-default`
  - Open Graph + Twitter cards; vast OG-beeld (bv. 1200×630) of dynamische `opengraph-image`
  - `metadata.robots` per route waar nodig (bv. `noindex` op 404)
  - Site-eigendom: `verification` (Google Search Console, Bing, …)
  - JSON-LD (Organization, WebSite, evt. BreadcrumbList)

- **Robots & sitemap**
  - `robots.ts` → `/robots.txt` (incl. link naar sitemap)
  - `sitemap.ts` → `/sitemap.xml` — alleen echte paden, geen `#fragmenten`; zelfde basis-URL als productie

- **App-tab & PWA-light**
  - Favicon / `icon` / `apple-icon`
  - Web App Manifest (`manifest.ts` of statisch `public/manifest.json`)
  - `viewport` + `themeColor` *(let op: `maximumScale: 1` beperkt zoom — a11y-afweging)*
  - Fonts: `next/font`, `display: swap`, geen overbodige weights
  - Thema-toggle (`next-themes`): waar nodig `suppressHydrationWarning` op `<html>`

- **Taal & routing** *(alleen als je i18n doet)*
  - `middleware` voor locale-prefix / redirects
  - `generateStaticParams` waar nodig
  - Correcte `<html lang>`

- **Privacy & meten**
  - Cookiemelding + consent-state; default denied tot keuze als je analytics gebruikt
  - GA4 alleen met ID + na toestemming; **één injectiepad** (niet tegelijk hardcoded in `<head>` én in een component)
  - Privacytekst dekt cookies / analytics mee

- **UX & performance**
  - `not-found.tsx`
  - `loading.tsx` (route loading UI) waar het de moeite is
  - `error.tsx` / `global-error.tsx` voor foutgrenzen (optioneel maar netjes)
  - Skip link naar `#main`
  - `next/image` met sizes / lazy waar passend
  - LCP-kritieke assets (vaak hero) comprimeren; lazy load onder de vouw
  - Periodiek **Tab** door primaire flows: zichtbare focus op nieuwe UI

- **Layout & design tokens**
  - Eén patroon voor **contentbreedte** (`max-w-*` + horizontale padding) zodat secties niet “springen”
  - **Eén kleurensysteem** (design tokens / CSS-variabelen); geen parallelle willekeurige palette-classes door de site
  - Productie-home **zonder** default Next/Vercel-templatemarketing en losse placeholder-assets als “site”


- **Next.js-config**
  - `images.formats` (bv. AVIF/WebP), `images.remotePatterns` voor externe URL’s
  - `experimental.optimizePackageImports` voor grote import-barrels (bv. iconen)

- **Integraties**
  - Contact/backend (bv. Formspree of eigen API): env voor keys; redirect- en return-URL’s op **hetzelfde productiedomein** als `metadataBase` / site-URL

- **Veiligheid & platform**
  - Security headers in `next.config` (CSP waar haalbaar; HSTS vaak via host)
  - `.env.example`; geen secrets in git; HTTPS + productiedomein
  - SPF, DKIM, DMARC als je mail vanaf het domein stuurt

- **Onderhoud**
  - CI: lint + build op push/PR
  - Lighthouse / Core Web Vitals vóór launch
  - Optioneel: error monitoring (bv. Sentry)
  - Af en toe: `npm audit` / Dependabot
  - Vóór launch: kritieke flows op **productie** testen (o.a. contact / mail); privacytekst juridisch laten nakijken indien nodig

- **Anti-pattern checks (snel nazien)**
  - Geen dubbele analytics-injectie (bv. niet tegelijk in `layout` én aparte component)
  - Geen hardcoded productie-URL’s in `layout`/`sitemap`; gebruik env + gedeelde helper
  - Geen `#fragment`-URL’s in `sitemap.xml`
  - Geen hardcoded keys/IDs in code; via env
