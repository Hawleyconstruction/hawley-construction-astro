# Hawley Construction Co. — Cloudflare Pages Deployment Guide

## What You Are Deploying

A fully static Astro site with:
- **25 pages** — all server-rendered to individual HTML files at build time
- **Per-page canonical tags** in the initial HTML (no JavaScript required)
- **Per-page meta titles and descriptions** in the initial HTML
- **Per-page JSON-LD structured data** (LocalBusiness, Service, FAQPage, Article schemas)
- **True HTTP 301 redirects** via `_redirects` file (processed at Cloudflare's edge)
- **GA4 + Google Ads tracking** on every page
- **Sitemap.xml** and **robots.txt**

---

## Step 1 — Push the Code to GitHub

1. Create a new GitHub repository (e.g., `hawley-construction-astro`)
2. From the `hawley-astro/` directory on your computer, run:
   ```bash
   git init
   git add .
   git commit -m "Initial Astro build"
   git remote add origin https://github.com/YOUR_USERNAME/hawley-construction-astro.git
   git push -u origin main
   ```

---

## Step 2 — Connect to Cloudflare Pages

1. Go to [dash.cloudflare.com](https://dash.cloudflare.com) → **Pages** → **Create a project**
2. Click **Connect to Git** → select your GitHub repository
3. Configure the build settings:

   | Setting | Value |
   |---|---|
   | **Framework preset** | Astro |
   | **Build command** | `pnpm build` |
   | **Build output directory** | `dist` |
   | **Node.js version** | 18 (or 20) |

4. Click **Save and Deploy**. Cloudflare will build and deploy the site in ~2 minutes.

---

## Step 3 — Connect Your Custom Domain

1. In Cloudflare Pages → your project → **Custom domains** → **Set up a custom domain**
2. Enter `hawleyconstruction.co`
3. If your domain is already on Cloudflare DNS, it will activate automatically.
4. If your domain is on another registrar (GoDaddy, Namecheap, etc.), update your nameservers to Cloudflare's nameservers (shown in the Cloudflare dashboard).

> **DNS propagation** typically takes 5-30 minutes when the domain is already on Cloudflare, or up to 48 hours if you are transferring nameservers.

---

## Step 4 — Verify the 301 Redirects

After the domain is live, test the redirects:

```bash
curl -I https://hawleyconstruction.co/services/kitchen-remodeling
# Expected: HTTP/2 301 → Location: /kitchen-remodeling-st-petersburg/

curl -I https://hawleyconstruction.co/services/bathroom-remodeling
# Expected: HTTP/2 301 → Location: /bathroom-remodeling-st-petersburg/
```

---

## Step 5 — Submit Sitemap in Google Search Console

1. Go to [search.google.com/search-console](https://search.google.com/search-console)
2. Select `hawleyconstruction.co`
3. Click **Sitemaps** → submit `https://hawleyconstruction.co/sitemap.xml`
4. Use **URL Inspection** on each of the 5 SEO landing pages → click **Request Indexing**

---

## Step 6 — Verify Tracking

1. Install [Google Tag Assistant](https://tagassistant.google.com/) Chrome extension
2. Visit `hawleyconstruction.co` — confirm both `AW-18172396876` and `G-1L7LLQBK32` fire on page load
3. Test the contact form at `/contact` — confirm the `generate_lead` conversion fires on submission
4. Click any phone number — confirm the `phone_call_click` conversion fires

---

## Future Deployments

Every time you push to the `main` branch on GitHub, Cloudflare Pages will automatically rebuild and redeploy the site. No manual steps required.

---

## File Structure Reference

```
hawley-astro/
├── src/
│   ├── layouts/
│   │   └── Layout.astro          ← Shared head: canonical, meta, schema, GA4, Ads
│   ├── components/
│   │   ├── Navbar.astro
│   │   └── Footer.astro
│   ├── pages/
│   │   ├── index.astro            ← Homepage
│   │   ├── contact.astro
│   │   ├── faq.astro
│   │   ├── our-work.astro
│   │   ├── services.astro         ← Services index
│   │   ├── services/              ← 6 service sub-pages
│   │   ├── kitchen-remodeling-st-petersburg/index.astro  ← SEO landing page
│   │   ├── bathroom-remodeling-st-petersburg/index.astro
│   │   ├── trex-deck-builder-tampa-bay/index.astro
│   │   ├── sunroom-florida-room-contractor/index.astro
│   │   ├── adu-in-law-suite-builder/index.astro
│   │   ├── areas/[city].astro     ← Tampa, St. Pete, Clearwater
│   │   └── blog/                  ← Blog index + 5 posts
│   └── styles/
│       └── global.css
├── public/
│   ├── _redirects                 ← HTTP 301 redirects (Cloudflare edge)
│   ├── sitemap.xml
│   └── robots.txt
├── astro.config.mjs
└── package.json
```
