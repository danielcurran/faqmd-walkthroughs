# gamemds

Walkthrough content for [gamemds.org](https://gamemds.org).

This repo is the **site** — it hosts walkthrough markdown files, the reader app,
the landing page, and GitHub Pages configuration. The converter tool that generates
walkthroughs lives in the separate **[faqmd](https://github.com/danielcurran/faqmd)** repo.

## Repository Roles

| Repo | Purpose |
|---|---|
| **[faqmd](https://github.com/danielcurran/faqmd)** | Converter tool + opencode agent skills |
| **gamemds** (this repo) | Walkthrough content hosted at [gamemds.org](https://gamemds.org) |

## Structure

| Path | Purpose |
|---|---|
| `guides.json` | Manifest of available walkthroughs |
| `guide/` | Walkthrough section files for the default/legacy guide |
| `guide/achievements.json` | RetroAchievements data — section mapping, missable cutoffs, strategic notes |
| `guide/achievements.md` | Auto-generated checklist with missable table + by-section view |
| `guides/<slug>/` | Walkthrough section files for additional games |
| `reader.html` | Walkthrough viewer app — renders guide sections with sidebar navigation, search, achievement badges, missable warnings, and progress tracking |
| `index.html` | Landing page at [gamemds.org](https://gamemds.org) |
| `marked.js` | Vendored markdown parsing library (loaded locally, not from CDN) |
| `CNAME` | Custom domain — `gamemds.org` |
| `.nojekyll` | Disables Jekyll preprocessing for GitHub Pages |
| `.github/workflows/deploy.yml` | GitHub Actions — deploys repo to GitHub Pages on push |

## Deploy

Push to `main` triggers the deploy workflow (`.github/workflows/deploy.yml`),
which deploys the repo root to the `gh-pages` branch. GitHub Pages then serves
the content at [gamemds.org](https://gamemds.org).

- **Trigger**: push to `main` or manual `workflow_dispatch`
- **Action**: `peaceiris/actions-gh-pages@v4` with `force_orphan: true`

## Adding or Updating a Walkthrough

1. Use the [faqmd](https://github.com/danielcurran/faqmd) tool to convert the walkthrough:
   ```bash
   git clone https://github.com/danielcurran/faqmd
   cd faqmd
   node scripts/convert.js "https://gamefaqs.gamespot.com/.../faqs/12345?print=1"
   ```
2. Optionally annotate with RetroAchievements via the opencode agent skill (produces `achievements.json`)
3. Optionally run quality passes:
   ```bash
   # (in opencode)
   "Run reformat-review on walkthrough.md"
   "Run art-modernize on walkthrough.md"
   ```
4. Split into sections (also generates `achievements.md` + updates `toc.json` if `achievements.json` exists):
   ```bash
   node scripts/split-guide.js walkthrough.md guide/
   ```
5. Copy the generated `guide/` directory into this repo under `guides/<slug>/` (or replace `guide/` for the legacy/default guide)
6. Add the new guide to `guides.json` with `slug`, `title`, `subtitle`, `author`, `desc`, and `path`. If the guide has RetroAchievements data, set `"hasAchievements": true`:
   ```json
   {
     "slug": "my-game",
     "title": "My Game",
     "subtitle": "Guide and Walkthrough",
     "author": "Author",
     "desc": "Guide and Walkthrough",
     "path": "guides/my-game",
     "hasAchievements": true
   }
   ```
7. Commit and push — the site auto-deploys

## Reader App Achievement Features

When a guide has `"hasAchievements": true` in `guides.json`, the reader app:

- **Inline badges** — Achievement badges (icon, title, points, missable indicator) appear at the top of each section that has associated achievements
- **Checkboxes** — Click a checkbox to mark an achievement as earned; progress persists in localStorage across sessions
- **Sidebar counter** — Shows "earned / total" progress in the sidebar below the table of contents
- **Missable warnings** — Yellow/red banners at the top of sections where missable achievements become unavailable
- **Checklist page** — Section `0.1 Achievement Checklist` in the TOC shows all achievements grouped by section with a missable table sorted by cutoff

## Related

- [faqmd](https://github.com/danielcurran/faqmd) — Converter tool + agent skills
