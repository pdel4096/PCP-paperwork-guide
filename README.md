# Paperwork & Forms: A Guide for PCPs

Practical guidance for family medicine on handling work-related patient
paperwork — FMLA, disability, accommodations, animals, jury duty,
death certificates, and the rest.

Written by Paul Delaney, MD. Practical guidance, not legal advice.
Views are the author's and do not represent his employer.

---

## Where everything lives

| What | URL |
| --- | --- |
| **Live site** | <https://pcp-paperwork-guide.pages.dev> |
| **Source repo** | <https://github.com/pdel4096/PCP-paperwork-guide> |
| **Cloudflare dashboard** | <https://dash.cloudflare.com> |
| **Build logs / deployments** | Cloudflare dashboard → Workers & Pages → pcp-paperwork-guide → Deployments |
| **Visitor analytics** | Cloudflare dashboard → Analytics & Logs → Web Analytics |
| **Local clone (Mac Mini)** | `/Users/delaneypa/GitHub Repos/PCP-paperwork-guide` |

**How the pieces fit together:** you edit Markdown files (on GitHub or
locally on the Mac). Every commit to `main` triggers Cloudflare Pages,
which runs MkDocs to build the site and publishes it — about 90 seconds
end to end. Cloudflare, not GitHub Actions, is what publishes the site.

Note that the **Metrics** tab inside the Pages project is *not* visitor
analytics — it reports Workers runtime stats (requests, CPU time) and will
read zero forever on a static site. Visitor data is under Analytics & Logs.

---

## Editing the site — two ways

### Way 1: On GitHub (quick edits, works from any device incl. phone)

1. On the live site, click the **pencil icon** at the top right of any page.
2. That opens the page's source file on GitHub.
3. Make the change.
4. Scroll down, click **Commit changes**, confirm the dialog.
5. Wait ~90 seconds, hard-refresh the live page (Cmd-Shift-R).

That pencil icon renders for *everyone*, not just you. A stranger who clicks
it gets sent to a sign-in page, or — if signed in — GitHub silently forks the
repo to their account and opens the editor there. They can only propose a pull
request; they cannot change this site. It's the standard "suggest an edit"
affordance and is worth keeping, given the invitation in the introduction.

### Way 2: On the Mac Mini (longer editing sessions)

The repo is cloned at `/Users/delaneypa/GitHub Repos/PCP-paperwork-guide`.
Authentication is stored in macOS Keychain (a GitHub Personal Access
Token — not the account password, which GitHub no longer accepts for git).

Edit the Markdown files in any plain-text editor (VS Code, Sublime,
Obsidian; if using TextEdit, Format → Make Plain Text first).

**To push changes:** right-click the repo folder (or any file in it) in
Finder → **Quick Actions → Update in GitHub**. A notification confirms
success or reports a problem. The Quick Action commits ALL changes in the
repo, then pulls and pushes.

> **Known limitation — the Quick Action skips brand-new files.**
> It stages modifications to files git already tracks, but ignores files
> that have never been committed. It reports "no local changes" and you
> think you've pushed when you haven't. **When you create a new file
> locally, push it from Terminal the first time:**
>
> ```
> cd "/Users/delaneypa/GitHub Repos/PCP-paperwork-guide"
> git add docs/path/to/newfile.md
> git commit -m "Add new page"
> git push
> ```
>
> After that first commit the file is tracked and the Quick Action handles
> it normally. To check for this before pushing, run `git status --short` —
> lines beginning `??` are untracked files the Quick Action will miss.

**Golden rule for two-device editing:** the Quick Action pulls before it
pushes, but if you edit the same file both on GitHub and locally between
syncs you can still create a conflict. Easiest habit: pick one place to
edit at a time, and run `git pull` before starting a local session.

MkDocs is **not** installed on the Mac Mini, so `mkdocs build` won't run
locally. Cloudflare is the only build. If you want a local preview before
pushing, that's a `pip install mkdocs-material` away.

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

3. Add the page to the `nav:` block at the bottom of `mkdocs.yml` — without
   this it exists but nothing links to it. Page entries sit at **six spaces**
   of indentation under their section heading, spaces not tabs:

   ```
     - Exams, licenses & obligations:
         - DOT physicals and certification exams: exams/dot-physicals.md
         - FAA medical exams for pilots: exams/faa-medical.md
   ```

   The path must match the file path exactly. A title containing a colon must
   be double-quoted.
4. If you created the file locally, see the Quick Action limitation above —
   push it with `git add` the first time.

**Do not** edit the old Word document and re-convert. That conversion is
lossy and reintroduces broken image paths and inline HTML. The Markdown
files in this repo are the source of truth now.

### Forcing the Mac to match GitHub (one-way, destructive)

When the local clone has drifted and you don't care about anything in it:

```
cd "/Users/delaneypa/GitHub Repos/PCP-paperwork-guide"
git fetch origin
git reset --hard origin/main
git clean -fd
```

`reset --hard` discards all local commits and edits; `clean -fd` deletes
untracked files and folders. **This is unrecoverable.** Run `git status`
first if there's any chance something local is worth keeping. Afterwards
`git status` should say the branch is up to date with nothing to commit.

---

## Search visibility

The site is currently **unlisted, not private**. Anyone with the link can
read the whole thing and forward it; the GitHub repo is public too,
including full Markdown source and commit history. There is no gate.

Well-behaved search engines are told to stay away, by two independent
mechanisms:

| Mechanism | File | Effect |
| --- | --- | --- |
| `robots.txt` | `docs/robots.txt` | asks crawlers not to fetch the site |
| `noindex` meta tag | `overrides/partials/integrations/analytics.html` | keeps pages out of results even if fetched |

**To become findable in search, BOTH must be reversed:** delete the
`<meta name="robots" content="noindex, nofollow">` line from
`analytics.html`, and either delete `docs/robots.txt` or change it to
`Disallow:` with no slash. Flipping only one accomplishes nothing.

Crawlers that ignore robots rules (scrapers, some AI training bots) take the
content regardless. Obscurity is not permission.

---

## Analytics

Cloudflare Web Analytics is enabled. The beacon script lives in
`overrides/partials/integrations/analytics.html`, which MkDocs Material
injects into the `<head>` of every page.

Two caveats when reading the numbers:

- **Ad blockers and privacy extensions block the beacon**, and this audience
  (physicians, residents, hospital networks) runs them more than average.
  Numbers are a floor and a trend line, not a census.
- The site is `noindex`, so organic search traffic will be zero by design.
  Empty graphs are not a verdict on the guide.

The beacon token is public by nature — it ships in the page source. It can be
regenerated from the Web Analytics dashboard if ever needed.

---

## Repo structure

```
docs/
  index.md                      # front page (introduction)
  robots.txt                    # crawler exclusion (see Search visibility)
  getting-started/
  leave-disability/             # FMLA, STD, work stress, SSDI, workers' comp
  accommodations/               # workplace, animals, school forms
  exams/                        # DOT, FAA, DMV, death certs, jury duty
  tools/                        # CoPilot, closing thought
  assets/                       # images
  stylesheets/extra.css         # sidebar styling
mkdocs.yml                      # site config + navigation order
overrides/partials/integrations/analytics.html   # noindex tag + CF beacon
```

Navigation order and grouping are controlled by the `nav:` block at the
bottom of `mkdocs.yml`, not by folder names.

---

## Troubleshooting

**Edits don't appear on the live site.** Almost always a failed build, which
leaves the previous version in place — so the site looks unchanged. Check
GitHub commit history: a red ✗ next to a commit = failed build. Read the log
at Cloudflare → Deployments → click the failed one.

Second most likely cause: the Quick Action silently skipped a new file. Run
`git status --short` and look for `??` lines.

**The #1 cause of failed builds: a YAML syntax error in `mkdocs.yml`.** A
colon inside a value breaks it. Any value with a colon must be double-quoted:

```
site_name: "Paperwork & Forms: A Guide for PCPs"   # correct
site_name: Paperwork & Forms: A Guide for PCPs     # breaks the build
```

This cost hours during setup. Check it first. Wrong indentation in the `nav:`
block is the runner-up — page entries take six spaces, and tabs are fatal.

**An image doesn't load.** Right-click → Copy image address, compare to where
the file actually sits. Path should be `../assets/filename.png` from a
subfolder page.

**Browser shows an old version.** Hard refresh (Cmd-Shift-R), or use a
private window to rule out cache.

**Git push asks for a password and rejects it.** GitHub wants the Personal
Access Token, not the account password — the prompt says "Password" but
means token. Username is `pdel4096` (no "n").

**Checking what's actually live.** From Terminal:

```
curl -s https://pcp-paperwork-guide.pages.dev/robots.txt
curl -s https://pcp-paperwork-guide.pages.dev/ | grep -i 'name="robots"\|cloudflareinsights'
```

---

## Still to do

- [ ] **Delete the redundant deploy workflow** — `.github/workflows/deploy.yml`
      is failing and emailing on every push. It does NOT publish the site
      (Cloudflare does). Delete it on GitHub to stop the emails.
- [ ] **Register a personal domain** and attach it: Cloudflare → Domain
      Registration (~$12/yr, WHOIS privacy free), then Workers & Pages →
      pcp-paperwork-guide → Custom domains. Update `site_url:` in `mkdocs.yml`.
      Do this before the pages.dev URL circulates widely. A real domain in the
      account also makes Web Analytics a toggle instead of a pasted token.
- [ ] **Decide on employer review** before circulating widely — a publicly
      readable clinical guidance site by an identifiable Atrium physician is
      easier to clear before than after. The footer disclaimer may or may not
      be sufficient.
- [ ] **Add a monthly link-checker** (Lychee GitHub Action) to catch dead
      government URLs and open an Issue when links rot.
- [ ] **Consider a "download as PDF" button** (mkdocs-with-pdf plugin) — nice
      for offline/print use, but build-fragile; pin the version if added.
- [ ] Add "last reviewed" dates to pages — rules change (see the 2025-26 HUD
      assistance-animal reversal, and the FAA's 2024 BasicMed expansion);
      readers need to know how stale content is.
- [ ] Fix undefined acronyms on first use (GINA, HUD, EEOC, FMCSA, DHHS, IEP,
      AOC, ESA, PHI, LLM); reconsider "CME" for *certified medical examiner*
      (collides with continuing medical education).
- [ ] Add a hero image for the FAA page in `docs/assets/`.
- [ ] Share with one resident for feedback before wider release.

### Done

- [x] Enable Cloudflare Web Analytics
- [x] Make embedded URLs clickable
- [x] Add FAA medical certification page
