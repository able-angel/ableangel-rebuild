# Able Angel Website — Operating Manual for Claude

This file is read automatically at the start of every Claude session in this repo. It contains the durable operational facts a new session needs to start correctly.

## What this repo is

The **Able Angel** nonprofit website — a static HTML/CSS site (no build system, no framework). Live at **https://ableangel.org**. Owned and managed by Peter York on behalf of the Able Angel board.

## Critical facts — read these first

- **Live branch: `claude/stoic-chebyshev`** — deploy from here, not `main`. `origin/main` is frozen at an ancient pre-Wix-migration state (only 2 commits) and should be ignored.
- **Hosting: Netlify.** Site ID: `348e09b2-be20-4d66-bae8-11efbdce8d68`. Project name in Netlify: `ableangel-preview`. Custom domain: `ableangel.org`.
- **GitHub account for push: `evalprof`** (NOT `pyork-project-evident`). The work account `pyork-project-evident` is active by default on this machine but does NOT have push access to the repo. If a push is rejected with HTTP 403, run `gh auth switch -u evalprof` and retry.
- **Email notifications for forms:** site-wide Netlify hooks already deliver every form submission to `kberger@able529.org` and `bnolan@able529.org`. New forms added to the site are automatically covered — no per-form notification setup required.

## Standard workflow for making a change

1. **Sync to live state first.** Before editing anything, run:
   ```
   git fetch origin && git reset --hard origin/claude/stoic-chebyshev
   ```
   This prevents working from a stale snapshot. (Previous sessions have gone astray by editing outdated code.)
2. **Edit files.**
3. **Preview locally** with a real HTTP server — do NOT rely on `file://` previews, which break CSS and images because browsers block sibling loads:
   ```
   python3 -m http.server 8765
   ```
   then open `http://localhost:8765` in a browser.
4. **Commit** with a descriptive message.
5. **Push to the live branch:**
   ```
   git push origin HEAD:claude/stoic-chebyshev
   ```
   Netlify auto-deploys within 1–2 minutes.
6. **If auto-deploy fails** (e.g. GitHub integration breaks again): fall back to a manual upload deploy from the local worktree:
   ```
   netlify deploy --prod --dir .
   ```

## Code patterns and key files

- **No templates.** Nav and footer are inlined on every HTML page. A nav-wide change = one edit per page. When adding a new page, copy nav and footer from an existing similar page (`able250.html` is a good template for a content-heavy page; `contact.html` is the template for a form page).
- **All styling is in `styles.css`.** Common reusable classes:
  - Layout: `page-banner`, `section`, `section-alt` (alternates white/grey backgrounds), `container`, `two-col`
  - Content blocks: `info-block`, `info-list` (checkmark bullets), `card-grid`, `card`, `values-grid`, `value-card`
  - Buttons: `btn-primary`, `btn-accent`, `btn-outline`
  - Home page: `announcement-banner`, `hero`, `hero-bg`, `hero-content`
  - Respect alternating `section` / `section-alt` pattern when inserting new sections — flip adjacent sections if needed to maintain white/grey rhythm.
- **Forms use Netlify Forms.** Pattern lives in `contact.html` and `declaration-review.html`:
  ```html
  <form name="form-name" method="POST" action="/thank-you-page.html"
        data-netlify="true" netlify-honeypot="bot-field">
    <input type="hidden" name="form-name" value="form-name">
    <p class="hidden" style="display:none;">
      <label>Don't fill this out: <input name="bot-field"></label>
    </p>
    <!-- your form fields -->
  </form>
  ```
  Site-wide email hooks already handle notifications for any new form.

## Existing pages and their roles

- `index.html` — home page with hero and top announcement banner
- `about.html` — mission, vision, values, board, sponsors
- `projects.html` — "The ABLE Act" (the main project — note nav calls it "The ABLE Act" but the filename is still `projects.html`)
- `able250.html` — the 250th anniversary campaign page
- `able250-contest.html` — the essay contest page (with Netlify submission form)
- `thank-you-contest.html` — post-submission confirmation for the essay contest
- `declaration-review.html` — preview + feedback form for the Declaration of Interdependence
- `thank-you.html` / `thank-you-declaration.html` — generic and declaration-specific form confirmations
- `resources.html` — curated external ABLE Act resources
- `blog.html` — blog index
- `contact.html` — general contact form

## Stakeholders

- **Peter York** (`pyork@projectevident.org`, GitHub `evalprof`): site owner and manager. Non-engineer. Explain technical concepts in plain English and verify state before taking irreversible actions.
- **Ken Berger**: board member; drives content and campaign direction.
- **Bill Nolan**: board member; co-recipient of form submissions.
- **Marilynn Knapp**: board member.

## Known unfinished items / future work

- **Email hosting migration:** Currently hosted at GoDaddy (~$400/yr for 4 mailboxes). Peter plans to recommend migrating to Google Workspace for Nonprofits (free for eligible 501(c)(3)s). Not a code change; track separately.
- **`contest@ableangel.org` email address:** Ken planned to set this up at GoDaddy as a forwarder to him and Bill. The site doesn't actually depend on this — submissions flow through Netlify Forms — but the address may still be wanted for external marketing materials.
