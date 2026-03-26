# Technisch / kwaliteit

*SEO, metadata, crawlers, prestaties, veiligheid, consent — wat bezoekers meestal niet als “feature” zien.*

- **Metadata & SEO**
  - `metadataBase` + productie-URL uit env
  - Titel-template; canonical; bij meertalig: hreflang / `x-default`
  - Open Graph + Twitter cards; vast OG-beeld (bv. 1200×630) of dynamische `opengraph-image`
  - JSON-LD (Organization, WebSite, evt. BreadcrumbList)

- **Robots & sitemap**
  - `robots.ts` → `/robots.txt` (incl. link naar sitemap)
  - `sitemap.ts` → `/sitemap.xml`

- **App-tab & PWA-light**
  - Favicon / `icon` / `apple-icon`
  - `manifest.ts`
  - `viewport` + `themeColor`
  - Fonts: `next/font`, `display: swap`, geen overbodige weights

- **Taal & routing** *(alleen als je i18n doet)*
  - `middleware` voor locale-prefix / redirects
  - `generateStaticParams` waar nodig
  - Correcte `<html lang>`

- **Privacy & meten**
  - Cookiemelding + consent-state; default denied tot keuze als je analytics gebruikt
  - GA4 alleen met ID + na toestemming
  - Privacytekst dekt cookies / analytics mee

- **UX & performance**
  - `not-found.tsx`
  - Skip link naar `#main`
  - `next/image` met sizes / lazy waar passend

- **Veiligheid & platform**
  - Security headers in `next.config` (CSP waar haalbaar; HSTS vaak via host)
  - `.env.example`; geen secrets in git; HTTPS + productiedomein
  - SPF, DKIM, DMARC als je mail vanaf het domein stuurt

- **Onderhoud**
  - CI: lint + build op push/PR
  - Lighthouse / Core Web Vitals vóór launch
  - Optioneel: error monitoring (bv. Sentry)
  - Af en toe: `npm audit` / Dependabot
