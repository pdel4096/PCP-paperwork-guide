# Paperwork & Forms: A Guide for PCPs

Practical guidance for family medicine on handling work-related patient
paperwork — FMLA, disability, accommodations, animals, jury duty,
death certificates, and the rest.

Written by Paul Delaney, MD. Practical guidance, not legal advice.
Views are the author's and do not represent his employer.

---

## Where everything lives

| What | URL |
|---|---|
| **Live site** | https://pcp-paperwork-guide.pages.dev |
| **Source repo** | https://github.com/pdel4096/PCP-paperwork-guide |
| **Cloudflare dashboard** | https://dash.cloudflare.com |
| **Build logs / deployments** | Cloudflare dashboard → Workers & Pages → pcp-paperwork-guide → Deployments |
| **Local clone (Mac Mini)** | `/Users/delaneypa/GitHub Repos/PCP-paperwork-guide` |

**How the pieces fit together:** you edit Markdown files (on GitHub or
locally on the Mac). Every commit to `main` triggers Cloudflare Pages,
which runs MkDocs to build the site and publishes it — about 90 seconds
end to end. Cloudflare, not GitHub Actions, is what publishes the site.

---

## Editing the site — two ways

### Way 1: On GitHub (quick edits, works from any device incl. phone)

1. On the live site, click the **pencil icon** at the top right of any page.
2. That opens the page's source file on GitHub.
3. Make the change.
4. Scroll down, click **Commit changes**, confirm the dialog.
5. Wait ~90 seconds, hard-refresh the live page (Cmd-Shift-R).

### Way 2: On the Mac Mini (longer editing sessions)

The repo is cloned at `/Users/delaneypa/GitHub Repos/PCP-paperwork-guide`.
Authentication is stored in macOS Keychain (a GitHub Personal Access
Token — not the account password, which GitHub no longer accepts for git).

Edit the Markdown files in any plain-text editor (VS Code, Sublime,
Obsidian; if using TextEdit, Format → Make Plain Text first).

**To push changes:** right-click the repo folder (or any file in it) in
Finder → **Quick Actions → Update in GitHub**. A notification confirms
success or reports a problem. The Quick Action commits ALL changes in the
repo, not just the file selected, then pulls and pushes.

If the Quick Action reports a pull conflict, resolve in Terminal:
```bash
cd "/Users/delaneypa/GitHub Repos/PCP-paperwork-guide"
git status
```

**Golden rule for two-device editing:** the Quick Action pulls before it
pushes, but if you edit the same file both on GitHub and locally between
syncs you can still create a conflict. Easiest habit: pick one place to
edit at a time.

---

## Common tasks

### Making a link clickable
Bare URLs are plain text until wrapped. Use the labeled form:
```
[SSA account](https://www.ssa.gov/myaccount)
```

### Adding an image
Put the file in `docs/assets/`, then from a page inside a subfolder:
```
![Alt text](../assets/filename.png)
```
Blank line above and below the image line.

### Adding a new page
1. Create `docs/<section>/<name>.md` (on GitHub: Add file → Create new file;
   slashes make folders).
2. Start it with:
   ```
   ---
   title: "Short title"
   ---

   # Short title
   ```
3. Add the page to the `nav:` block in `mkdocs.yml` — without this it exists
   but nothing links to it.

**Do not** edit the old Word document and re-convert. That conversion is
lossy and reintroduces broken image paths and inline HTML. The Markdown
files in this repo are the source of truth now.

---

## Repo structure

```
docs/
  index.md                      # front page (introduction)
  getting-started/
  leave-disability/             # FMLA, STD, work stress, SSDI, workers' comp
  accommodations/               # workplace, animals, school forms
  exams/                        # DOT, DMV, death certs, jury duty
  tools/                        # CoPilot, closing thought
  assets/                       # images
  stylesheets/extra.css         # sidebar styling
mkdocs.yml                      # site config + navigation order
overrides/                      # theme overrides (analytics partial)
```

Navigation order and grouping are controlled by the `nav:` block at the
bottom of `mkdocs.yml`, not by folder names.

---

## Troubleshooting

**Edits don't appear on the live site.**
Almost always a failed build, which leaves the previous version in place —
so the site looks unchanged. Check GitHub commit history: a red ✗ next to a
commit = failed build. Read the log at Cloudflare → Deployments → click the
failed one.

**The #1 cause: a YAML syntax error in `mkdocs.yml`.** A colon inside a
value breaks it. Any value with a colon must be double-quoted:
```yaml
site_name: "Paperwork & Forms: A Guide for PCPs"   # correct
site_name: Paperwork & Forms: A Guide for PCPs     # breaks the build
```
This cost hours during setup. Check it first.

**An image doesn't load.** Right-click → Copy image address, compare to where
the file actually sits. Path should be `../assets/filename.png` from a
subfolder page.

**Browser shows an old version.** Hard refresh (Cmd-Shift-R), or use a
private window to rule out cache.

**Git push asks for a password and rejects it.** GitHub wants the Personal
Access Token, not the account password — the prompt says "Password" but
means token. Username is `pdel4096` (no "n").

---

## Still to do (next session)

- [ ] **Delete the redundant deploy workflow** — `.github/workflows/deploy.yml`
      is failing and emailing on every push. It does NOT publish the site
      (Cloudflare does). Delete it on GitHub to stop the emails.
- [ ] **Register a personal domain** and attach it: Cloudflare → Domain
      Registration (~$12/yr, WHOIS privacy free), then Workers & Pages →
      pcp-paperwork-guide → Custom domains. Update `site_url:` in mkdocs.yml.
      Do this before the pages.dev URL circulates widely.
- [ ] **Enable Cloudflare Web Analytics** — dashboard → Analytics & Logs →
      Web Analytics → add site → paste token into
      `overrides/partials/integrations/analytics.html`.
- [ ] **Make embedded URLs clickable** — nine gov/legal links currently plain
      text (DOL x2, SSA, ADA, fairhousingnc, EEOC, NC Courts, NCDOT, NC
      Industrial Commission). Optionally add a script to open external links
      in a new tab.
- [ ] **Add a monthly link-checker** (Lychee GitHub Action) to catch dead
      government URLs and open an Issue when links rot.
- [ ] **Consider a "download as PDF" button** (mkdocs-with-pdf plugin) — nice
      for offline/print use, but build-fragile; pin the version if added.
- [ ] Add "last reviewed" dates to pages — rules change (see the 2025-26 HUD
      assistance-animal reversal); readers need to know how stale content is.
- [ ] Fix undefined acronyms on first use (GINA, HUD, EEOC, FMCSA, DHHS, IEP,
      AOC, ESA, PHI, LLM); reconsider "CME" for *certified medical examiner*
      (collides with continuing medical education).
- [ ] Share with one resident for feedback before wider release.
```
