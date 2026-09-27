# anshsaxena05.github.io

Personal site of **Ansh Saxena**, Backend & ML Infrastructure Engineer at Cyware Labs, Bengaluru.

Live at: https://anshsaxena05.github.io

## What's here

It is plain static HTML and CSS, with no build step and no JavaScript.

| Path | Purpose |
|------|---------|
| `index.html` | Home: about, experience, projects, skills, writing, education, contact. Includes schema.org `Person` JSON-LD |
| `projects/soc-triage-agent.html` | Case study for [cyberSecurity_alert_triage](https://github.com/AnshSaxena05/cyberSecurity_alert_triage) |
| `writing/index.html` | Index of published articles |
| `style.css` | Shared styles, including light/dark mode |
| `favicon.svg` | Site icon |
| `404.html` | GitHub Pages not-found page |
| `llms.txt` | Site index for LLMs and AI assistants ([llmstxt.org](https://llmstxt.org/) format) |
| `llms-full.txt` | Full text of every page as one Markdown file |
| `projects/llms.txt`, `writing/llms.txt` | Section-level llms.txt files (the spec allows one per sub-path) |
| `*.html.md` | Clean Markdown copy of each page, at the page URL + `.md`, linked from each page with `rel="alternate" type="text/markdown"` |
| `og-image.png`, `apple-touch-icon.png` | 1200×630 social preview image and PNG icon |
| `598f27c9450b762556e410ab0c16d2ea.txt` | IndexNow key (lets Bing and other IndexNow engines accept URL submissions for this site). Do not delete |
| `google1363753c28a7dd7a.html` | Google Search Console ownership verification. Do not delete, or the site becomes unverified |
| `robots.txt`, `sitemap.xml` | Crawl directives and sitemap. robots.txt only works at the root, so there is one for the whole site |
| `.nojekyll` | Serve files as-is (skip Jekyll) |

## Editing

1. Edit the HTML directly.
2. When you add or change a page, update `sitemap.xml` (`lastmod`), the page's `.html.md` copy, `llms.txt` and `llms-full.txt`, and `dateModified` in the page's JSON-LD. Use a full ISO 8601 datetime with offset (e.g. `2026-09-27T18:45:00+05:30`); Search Console flags a bare date as "Invalid datetime value".
3. Keep facts in sync with the GitHub profile README and LinkedIn.

## Deploy

1. Push to `main` of the `AnshSaxena05.github.io` repo.
2. Under **Settings → Pages**, set the source to "Deploy from a branch", branch `main`, folder `/ (root)`.
