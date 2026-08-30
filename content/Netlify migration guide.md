# Migrating Hermenoiesis from GitHub Pages to Netlify (private repo)

- created: 260829, 05:25
- updated:  
- by: Claude Sonnet 5
- context: [[content/index|index]]

---


Goal: keep the GitHub repo private (source, vault, history hidden) while
still publishing the built Quartz site publicly, for free, via Netlify.

## Why this works

- **GitHub (private repo)** — vault home: version control, sync, full
  history, nothing publicly visible.
- **Quartz** — the build step, unchanged. Same `quartz.config.yaml`,
  same `content/` folder.
- **Netlify (free tier)** — the host. Watches the private GitHub repo,
  runs the Quartz build on its own infrastructure on every push, and
  serves the result at a public `*.netlify.app` URL.

GitHub Pages requires a public repo (or a paid plan) to serve a site.
Netlify has no such restriction — private repos deploy to a public
site on its free tier.

## Steps

### 1. Make the GitHub repo private

- Go to the repo → **Settings → General**.
- Scroll to **Danger Zone → Change repository visibility → Make private**.
- This is allowed on GitHub Free at any time — the restriction only
  applies to *GitHub Pages*, not to repo visibility itself.

### 2. Connect Netlify

- Sign up at [netlify.com](https://netlify.com), log in with GitHub.
- **Add new site → Import an existing project**.
- Authorize Netlify to access the (now private) repo, and select it.

### 3. Configure the build

When Netlify asks for build settings, use:

- **Build command:** `npx quartz build`
- **Publish directory:** `public`

### 4. Deploy

- Trigger the first deploy. Netlify assigns a URL immediately
  (something like `random-name-123.netlify.app`).
- Every future `git push` will trigger an automatic rebuild, the same
  way the GitHub Actions workflow used to.

### 5. Update `baseUrl`

In `quartz.config.yaml`, update:

```yaml
baseUrl: your-site-name.netlify.app
```

(or your custom domain, if you set one up later in Netlify's
**Domain settings**). This is the same field that caused the graph
404 bug on GitHub Pages — skipping this step would reproduce the same
issue on the new domain.

### 6. Retire the GitHub Pages workflow

Once the Netlify deploy is confirmed working:

- Delete or disable `.github/workflows/deploy.yml` (or whichever
  workflow file drives the GitHub Actions build).
- Go to **Settings → Pages** in the GitHub repo and turn Pages off,
  since Netlify now owns the build-and-deploy job.

## Checklist

- [ ] Repo set to private
- [ ] Netlify site created and linked to the repo
- [ ] Build command `npx quartz build` set
- [ ] Publish directory `public` set
- [ ] First deploy successful
- [ ] `baseUrl` updated in `quartz.config.yaml` to the new Netlify domain
- [ ] Graph view tested on the new URL (no 404s on node clicks)
- [ ] Old GitHub Actions workflow disabled/deleted
- [ ] GitHub Pages turned off in repo settings
