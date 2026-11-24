# www.enterprise-r.com

The Enterprise-R website. Public repo (needed for GitHub Pages on the Free tier). Built with [Astro](https://astro.build), plain HTML/CSS, no frameworks.

Part of [enterprise-r/project-foundation](https://github.com/enterprise-r/project-foundation). Brand files come from [enterprise-r/project-branding](https://github.com/enterprise-r/project-branding). The imported files are recorded in `vendor/MANIFEST`.

Pages: Home (`/`), About (`/about/`), Login (`/r/`), 404 (`/404.html`) and `/showcase/`. The showcase is the web UI, linked from the login page. It is `noindex` and not in the sitemap. `/login/` redirects to `/r/`. Nav links say Login. On `/r/` the breadcrumb says Login until Enter, then `demo@ra.hq`.

## Where things are

| To change… | Edit |
|---|---|
| Page text (title, heading, contact email) | the `page` block at the top of the page in `src/pages/` |
| Logo and favicons | `public/assets/`. The root breadcrumb shows `logo-wide-black.svg` from 48rem and `logo-compact-black.svg` below |
| Styles | `src/styles/`: `palette.css` (colours), `theme/` (Dark-R), `bar.css` (title bar), `footer.css`, `home.css`, `about.css`, `login.css`, `showcase.css` (style guide) |
| Shared head, title bar and navigation | `src/layouts/Base.astro` |
| Social links | the `social` list in `src/components/Footer.astro` |
| Showcase sections and tutorial notes | `src/components/showcase/`, one component per section, put in order by `src/pages/showcase.astro` |
| Home terminal | `src/components/HomeWait.astro` and `src/styles/home.css` |
| Page not found | `src/pages/404.astro` and `src/components/NotFound.astro`. The look is `src/styles/home.css` |
| Search engines | `src/pages/robots.txt.ts`, and the indexed pages in `src/pages/sitemap.xml.ts` |
| Fonts | `src/styles/fonts/woff2/`. The theme loads them from `../fonts/woff2/` |
| Custom domain | repo Settings → Pages → Custom domain (Pages source is GitHub Actions, so there is no `CNAME` file) |

A new page is a file in `src/pages/` that uses the `Base` layout. About imports `src/styles/about.css`. Login imports `src/styles/login.css`.

## Sites

The same code serves two sites from two repositories:

| Repository | Site | Deploys from |
|---|---|---|
| `enterprise-r/www-prod` | `https://www.enterprise-r.com/` (production) | `main` |
| `enterprise-r/www-ng` | `https://ng.enterprise-r.com/` (staging preview) | `www` |

Work on the site is the recurring mani work line `www` in `www-ng`. `mani run branch -p www-ng BRANCH=www` opens it from `main`, or tracks `origin/www`. Every push to `www` publishes the preview. `mani run pr www-ng=<issue> APPLY=1` opens the `www` → `main` pull request, and landing it promotes: `.github/workflows/promote.yml` fast-forwards `www-ng` `main` to `www-prod` `main`, which then deploys. The push never forces. If `www-prod` `main` has moved ahead, the promotion fails and the two `main` branches need reconciling by hand. A ruleset blocks deleting `www`, so `mani run close` deletes only the local branch, and the next `branch` tracks `origin/www` again. On a `www-ng` checkout, `origin` is `www-ng` and `upstream` is `www-prod`.

What differs between the sites is not committed, so a promotion cannot carry it. Each repository sets it in Settings → Secrets and variables → Actions:

| Variable | Production | Preview |
|---|---|---|
| `SITE_URL` | `https://www.enterprise-r.com` | `https://ng.enterprise-r.com` |
| `DEPLOY_BRANCH` | `main` | `www` |
| `SITE_NOINDEX` | unset | `true`: every page `noindex`, and `robots.txt` disallows all with no sitemap |
| `PROMOTE_TO` | unset | `enterprise-r/www-prod` |

The preview also holds the secret `PROMOTE_TOKEN`, a token with Contents write on `www-prod`. Company links in the content (QR, footer, contact) stay `www.enterprise-r.com` on both sites.

## Deploy

A push or a `/fast-forward` land to the branch in `DEPLOY_BRANCH` runs `.github/workflows/deploy.yml`, which builds the site and publishes it to GitHub Pages in about a minute. Files can be edited on github.com. A CI build without `SITE_URL` fails. Settings → Pages → Source must be **GitHub Actions**.

## Local (optional, Node 24)

```sh
npm ci
npm run dev      # http://127.0.0.1:4321
npm run build    # output in dist/; SITE_URL defaults to http://127.0.0.1:4321
```

## Pages

The bar is the theme's app bar (`theme/bar.css`): breadcrumbs on the left, links on the right. The root crumb is the black logo, as tall as the crumb: wide from 48rem, compact below, on the theme's root crumb fill and hover. On Home that crumb is the page heading (`h1`) and does not navigate. Breadcrumbs show the active page. A page that also has a nav button leaves the nav while you are on it and shows there as the self-link. On Home the right nav is About and Login. On About it is Login. On Login it is About.

hyper-j is the site wallpaper, set once in `bar.css`. The style guide and the login page keep the flat page colour. Home and the 404 each have their own terminal (`HomeWait.astro`, `NotFound.astro`). They share the look (`home.css`) and a mote field (`Motes.astro`). The 404 turns the terminal 45 degrees. About's plates are in `about.css`. About shows the QR code small at the bottom left; a click opens it large in a dialog, and a click, Escape or the backdrop closes it.

The footer is a slim stripe: LinkedIn, X and GitHub, inline paths from Simple Icons (CC0). On a short page it sits at the bottom of the window. On the wallpaper the stripe is open and the icons switch to Foreground, so they stay legible on the page behind the stripe.
