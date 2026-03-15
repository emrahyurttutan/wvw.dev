<p align="center">
  <img src="logo.png" width="128" height="128" alt="World Vibe Web">
</p>

<h1 align="center">World Vibe Web</h1>

<p align="center">
  <em>The distributed app store for vibe-coded projects.</em>
</p>

<p align="center">
  <a href="https://wvw.dev">Website</a> ·
  <a href="https://github.com/f/wvw.dev-app/releases/latest">Desktop App</a> ·
  <a href="#publish-your-apps">Publish Your Apps</a> ·
  <a href="https://github.com/f/wvw.dev/blob/master/DISTRIBUTE.md">Full Guide</a>
</p>

---

World Vibe Web is a **federated app store** for open-source, vibe-coded projects. There is no central authority — anyone with a GitHub repo can publish apps. WVW aggregates them all into one browsable catalog at [wvw.dev](https://wvw.dev).

No gatekeeping. No review queue. No 30% cut. Just open-source apps, discoverable in one place.

## The Architecture

WVW follows a federated model. Each developer or team maintains their own `apps.json` in their own repo. WVW doesn't host your apps — it indexes them.

```
Your repo (apps.json)    ─┐
Another repo (apps.json)  ├──→  build.sh  ──→  unified apps.json  ──→  wvw.dev
More repos (apps.json)   ─┘
```

1. **`stores.json`** lists all registered store sources — GitHub repo paths or direct URLs
2. A GitHub Action runs **`build.sh`** every 6 hours, fetching each store's `apps.json`
3. Apps are merged, deduplicated, enriched with live GitHub stats, and written to a unified `apps.json`
4. The static site reads this file and renders the catalog — no server, no database

Every store is equal. Your apps sit alongside everyone else's, sorted by merit, not by who paid more.

## Desktop App

WVW has a native desktop app built with **Tauri + Rust** — no Electron, no web views pretending to be native.

```bash
brew install f/tap/wvw
```

Available for **macOS** (Apple Silicon + Intel), **Windows**, and **Linux**. Downloads at [github.com/f/wvw.dev-app/releases](https://github.com/f/wvw.dev-app/releases/latest).

The app loads wvw.dev in a native window with macOS title bar integration, sidebar drag regions, and external links opening in your default browser.

## PWA

Don't want to install anything? WVW is also a **Progressive Web App**. Visit [wvw.dev](https://wvw.dev) in Chrome or Safari and install it to your home screen. Works offline.

## Guardrails

WVW applies guardrails during every build to keep the catalog trustworthy:

| What | Rule |
|------|------|
| **Stars & forks** | Declared values are **ignored** — WVW fetches live counts from the GitHub API. You cannot inflate your stats. |
| **Featured apps** | Your `featured` array is **ignored** — WVW maintains its own `featured.json` curated by maintainers. |
| **Categories** | Only categories from WVW's `categories.json` are accepted. Unrecognized ones are stripped. Apps with zero valid categories are excluded. |
| **Owner attribution** | Every app gets an owner badge derived from the GitHub repo path, not from your `apps.json`. |
| **Source tracking** | The detail page shows "Published by" and "Source Store" — injected by the build, not declared by you. |
| **Deduplication** | If two repos declare the same app `id`, only the first is kept. |

## App Security

Every app listed on WVW may be **vibe-coded** — possibly built with AI-assisted development. WVW does not audit, verify, or guarantee the safety of any listed app.

Before installing any app:
- **Review the source code** — every listing links directly to its GitHub repo
- **Run in sandboxed environments** when possible
- **Install at your own risk**

WVW is a directory, not a distributor. Responsibility for each app belongs to its developer.

## AI-Powered Automation

WVW runs on GitHub Agentic Workflows and AI at every layer — no human curation bottleneck.

### Featured Apps — Selected by AI

Featured apps in the homepage carousel are **curated daily by an AI agent** via [GitHub Agentic Workflows](https://github.github.com/gh-aw/). Every day, a Copilot-powered agent:

1. Reads the full app catalog
2. Selects 4 apps from different stores, rotating picks to ensure variety
3. Writes editorial-quality headlines, titles, and subtitles in Apple App Store voice
4. Opens a pull request with the updated `featured.json`

No human picks the featured apps. The agent balances high-star anchors with hidden gems and fresh submissions.

### Weekly Showcase — AI-Generated Banners

Every Monday, `showcase.sh` runs and:

1. **Randomly selects 2 apps per category** from the catalog
2. For each picked app, calls [fal.ai](https://fal.ai)'s **Nano Banana 2** model to generate a unique promotional banner image — using the app's icon as a reference when available
3. Each banner is styled differently (3D renders, isometric, flat vector, neon, watercolor, paper cut-out) so no two look the same
4. The generated images appear as showcase cards on category pages, store detail pages, and in the "Must-Try Apps" section on Discover

This means every app in the catalog has a chance to be spotlighted with a professionally generated banner — fully automated, refreshed weekly.

### Automated PR Reviews

When someone opens a PR to add their store to `stores.json`, a GitHub Action automatically:

1. Fetches their `apps.json` from the submitted repo
2. Runs static validation — required fields, category checks, URL formats, wording quality
3. Calls the **Copilot API** for AI-powered editorial suggestions on subtitles, descriptions, categories, and icon styling
4. Posts a structured review comment on the PR with issues, suggestions, and AI recommendations

The submitter gets instant, actionable feedback without waiting for a maintainer.

## Publish Your Apps

1. Create an `apps.json` in your repo following the [Appétit schema](https://wvw.dev/apps.schema.json)
2. Open a PR adding your repo to [`stores.json`](stores.json):

```json
[
  "yourname/your-repo"
]
```

3. The review bot comments with feedback. Once merged, your apps appear on wvw.dev within 6 hours.

See the [full distribution guide](DISTRIBUTE.md) for schema details, icon styling, install buttons, and more.

## Run Locally

```bash
git clone https://github.com/f/wvw.dev.git
cd wvw.dev
./build.sh
python3 -m http.server 8080
```

## Fully Static

The entire store — catalog, app detail pages, store pages, search — is a **static site hosted on GitHub Pages**. There is no server, no database, no backend. Just HTML, CSS, JS, and JSON files served from a CDN.

Every 6 hours, a GitHub Action rebuilds the catalog. Every day, an AI agent curates the featured apps. Every week, another action generates fresh showcase banners. All of it committed as flat files and served statically. The site works offline via service worker, supports clean URLs via a `404.html` redirect trick, and is installable as a PWA.

Zero infrastructure costs. Zero maintenance burden. The entire app store runs on GitHub's free tier.

## Built With

- **Frontend**: Vanilla HTML/CSS/JS — no frameworks, no build step
- **Build**: Bash + jq
- **Desktop App**: [Tauri](https://tauri.app) + Rust
- **Featured Curation**: [GitHub Agentic Workflows](https://github.github.com/gh-aw/) (daily AI agent)
- **Showcase Banners**: [fal.ai](https://fal.ai) Nano Banana 2 (weekly AI image generation)
- **PR Reviews**: GitHub Copilot API (automated editorial feedback)
- **Hosting**: GitHub Pages
- **Everything**: Built with agentic engineering and vibecoding

## License

MIT
