# Technisch / kwaliteit

*SEO, metadata, crawlers, prestaties, veiligheid, consent — wat bezoekers meestal niet als “feature” zien. Dit bestand is een **checklist-sjabloon**: kopieer naar een nieuw project en vink af (`[ ]` → `[x]`).*

---

## Metadata & SEO

- [ ] **`metadataBase`** via één gedeelde helper (bv. `metadataBaseFromSiteUrl()` / `new URL(siteUrl)`) — productie-URL uit env (`NEXT_PUBLIC_SITE_URL` of vast in `config/site.ts`), geen losse strings in elk layout.
- [ ] **Titel**: `title.default` + `title.template` (`"%s | SiteNaam"`) in root- of locale-layout.
- [ ] **Canonical + hreflang** (alleen bij meertalig):
  - `alternates.canonical`: `/${locale}/…`
  - `alternates.languages`: `nl`, `en`, `x-default` (richt `x-default` bewust, vaak naar primaire taal).
  - Helper `buildLocaleAlternates(locale, pathAfterLocale)` hergebruiken per route.
- [ ] **Open Graph**: `type`, `url`, `siteName`, `title`, `description`, `locale` (bv. `nl_BE` / `en_US`).
  - [ ] **`alternateLocale`**: andere taal expliciet zetten (bv. NL-pagina → `["en_BE"]`) — helpt social previews.
- [ ] **OG-afbeelding** (minimaal één van):
  - **Dynamisch** (aanbevolen bij i18n): `app/[locale]/opengraph-image.tsx` met `ImageResponse`, `size` 1200×630, `contentType` `image/png`.
  - **Statisch**: asset 1200×630 onder `public/` + in metadata **`width` + `height` + `alt`** op `openGraph.images`.
- [ ] **Twitter**: `card: "summary_large_image"`, zelfde titel/beschrijving; `images` mag absolute URL zijn (consistent met `SITE_URL` + pad naar OG).
- [ ] **`metadata.robots`**: op `not-found` vaak `noindex` (en eventueel `nofollow`); overige routes `index` tenzij bewust anders.
- [ ] **Site-eigendom**: `verification.google` (en evt. Bing) via env, bv. `NEXT_PUBLIC_GOOGLE_SITE_VERIFICATION` — alleen de **content**-waarde van de meta-tag, geen volledige HTML.
- [ ] **JSON-LD**: minimaal `Organization` + `WebSite` (`logo`, `url`, `email` waar van toepassing); optioneel `BreadcrumbList` op diepe pagina’s.

---

## Robots & sitemap

- [ ] **`src/app/robots.ts`** → `/robots.txt` met `sitemap: ${SITE_URL}/sitemap.xml` (of env-basis-URL).
- [ ] **`src/app/sitemap.ts`** → `/sitemap.xml`:
  - Alle **indexeerbare** routes, incl. **alle taalvarianten** (`/nl/...`, `/en/...`) en subpagina’s (bv. `/peers`, `/privacy`) als die eigen URL’s hebben.
  - Geen `#fragmenten`; zelfde host als productie.
  - Bij nieuwe route: sitemap **meesturen**.

---

## App-tab & PWA-light

- [ ] **Favicon**: `app/icon.tsx` / `app/apple-icon.tsx` of vaste assets in `public/`.
- [ ] **`manifest.ts`** (of `public/manifest.webmanifest`) — `name`, `start_url`, `theme_color`, `icons`.
- [ ] **`viewport`** + **`themeColor`**; geen `maximumScale: 1` tenzij bewuste keuze (beperkt zoom → a11y).
- [ ] **Fonts**: `next/font`, `display: "swap"`, geen overbodige weights importeren.
- [ ] **`next-themes`**: alleen als je dark mode doet; anders `suppressHydrationWarning` op `<html>` alleen waar nodig (bv. sync met `lang`).

---

## Taal & routing *(Next.js App Router + i18n)*

- [ ] **`src/i18n/config.ts`**: `locales`, `defaultLocale`, `isLocale()`.
- [ ] **`src/messages/`**: `getMessages(locale)` + `nl`/`en` (of TS-modules); geen losse JSON + client-only taalwissel als je URL-talen wilt (SEO).
- [ ] **`LocaleProvider` + `useLocaleContext()`** voor clientcomponenten; server: `getMessages` direct.
- [ ] **`middleware.ts`**: `/` → `/${defaultLocale}`; onbekende paden naar `/${defaultLocale}/…`.
  - [ ] **Root-metadata uitzonderingen**: segmenten als `icon`, `apple-icon`, `opengraph-image`, `twitter-image`, `manifest.webmanifest` **geen** locale-prefix (anders breekt favicon/OG).
- [ ] **`generateStaticParams`** op `[locale]` (en dynamische segmenten).
- [ ] **`<html lang>`**: client `HtmlLangSync` op basis van eerste path-segment, of server alleen als je geen client nodig hebt.
- [ ] **Taalwissel**: `Link` naar andere locale (`/${andereTaal}${tail}`), geen alleen `localStorage` zonder URL.

---

## Privacy & meten

- [ ] Cookiemelding + **consent-state**; analytics pas na expliciete opt-in als je wettelijk/consent-gedreven werkt.
- [ ] **GA4**: één injectiepad (één component); geen dubbele tag in layout + apart script.
- [ ] Privacytekst dekt **cookies**, **analytics** en contactformulier mee.

---

## UX & performance

- [ ] `not-found.tsx` (link naar home in default-taal).
- [ ] `loading.tsx` / `error.tsx` / `global-error.tsx` waar zinvol.
- [ ] **Skip link** naar `#main` (label uit vertalingen).
- [ ] `next/image`: `sizes`, `priority` alleen waar LCP; rest lazy.
- [ ] Periodiek **Tab** door primaire flows (focus zichtbaar).

---

## Layout & design tokens

- [ ] Eén **contentbreedte** (`max-w-*` + horizontale padding) voor secties.
- [ ] Eén **kleurensysteem** (CSS-variabelen / design tokens).
- [ ] Geen zichtbare Next.js / Vercel-startercopy of placeholder-assets als “definitieve” site.

---

## Next.js-config

- [ ] `images.formats` (AVIF/WebP), `images.remotePatterns` voor externe `next/image`-URL’s.
- [ ] `experimental.optimizePackageImports` voor grote barrels (bv. `lucide-react`).
- [ ] **Security headers** in `next.config` (X-Frame-Options, Referrer-Policy, …); CSP/HSTS naar team/host.

---

## Integraties

- [ ] Formspree/API: keys uit env; **`_next`** / redirect-URL’s op **hetzelfde productiedomein** als `SITE_URL`.
- [ ] Mailto-links: testen op mobiel/desktop.

---

## Veiligheid & platform

- [ ] Publieke “config” (site-URL, analytics-ID, Formspree-ID, verificatie-tokens) centraal (bv. `src/config/site.ts`); **geen** echte secrets in git.
- [ ] `.env.example` met alle `NEXT_PUBLIC_*` die je gebruikt; secrets alleen in hosting / `.env.local`.
- [ ] HTTPS op productie; SPF/DKIM/DMARC als je mail vanaf het domein stuurt.

---

## Onderhoud

- [ ] CI: **lint + build** op push/PR.
- [ ] Vóór launch: Lighthouse / Core Web Vitals; kritieke flows op **productie** (contact, taalwissel, formulier).
- [ ] Optioneel: Sentry / error monitoring.
- [ ] Af en toe: `npm audit`, Dependabot.

---

## Anti-patterns (snel nazien vóór merge)

- [ ] Geen **dubbele analytics**-injectie.
- [ ] Geen hardcoded productie-URL in sitemap/metadata — gebruik env + helpers.
- [ ] Geen `#fragment`-URL’s in `sitemap.xml`.
- [ ] Geen API-keys of secrets in broncode; alleen env.
- [ ] **Sitemap**: bij nieuwe publieke pagina met eigen metadata → ook in `sitemap.ts` opnemen.

---

## Referentie: projecten in deze workspace

| Patroon | Voorbeeld |
|--------|-----------|
| i18n + messages + OG dynamisch | `atlas-coding-web`, `roots-connect-web` |
| Meertalig + services + checklist met vinkjes | `elbm-cleaning-web/TECHNICAL_TODO.md` (projectspecifiek) |

Pas deze lijst per klant aan; verwijder secties die niet van toepassing zijn (bv. i18n bij enkel-talige site).
