# PCP paperwork guide

Practical guidance for family medicine on handling work-related patient
paperwork — FMLA, disability, accommodations, animals, jury duty,
death certificates, and the rest.

Written by Paul Delaney, MD. Views are the author's and do not
represent his employer. Practical guidance, not legal advice.

## Editing content

Every page is a plain-text Markdown file under `docs/`. To edit:

1. Open the file on github.com (click the pencil icon on any page of
   the live site — it takes you straight to the source).
2. Make your changes and commit.
3. Wait ~1 minute for GitHub Actions and Cloudflare Pages to rebuild.
   The live site updates automatically.

No local setup needed for small edits. For bigger changes, install
MkDocs locally (`pip install mkdocs mkdocs-material pymdown-extensions`)
and run `mkdocs serve` for a live preview on `http://localhost:8000`.

## Structure

```
docs/
  index.md                       # front page (introduction)
  getting-started/
  leave-disability/              # FMLA, STD, SSDI, workers' comp, etc.
  accommodations/                # workplace, animals, school forms
  exams/                         # DOT, DMV, death certs, jury duty
  tools/                         # CoPilot, closing thought
  assets/                        # images (Sankey, etc.)
mkdocs.yml                       # site config and navigation
overrides/                       # theme overrides (analytics partial)
.github/workflows/deploy.yml     # CI: build + deploy on every push
```

Section order in the top navigation is set by the `nav:` block in
`mkdocs.yml`. To add a new page: create the Markdown file, then add
it to `nav:` under the appropriate tab.

## Analytics

Cloudflare Web Analytics is wired in but disabled by default. To turn
it on, follow the comment inside
`overrides/partials/integrations/analytics.html` — paste your
Cloudflare token, commit, done.

Local editing set up on Mac Mini, August 2026.

Local editing set up on Mac Mini, August 2026.

Local editing set up on Mac Mini, August 2026.
