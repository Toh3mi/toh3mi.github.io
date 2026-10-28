# Service Reachability Monitor

A single self-contained static page. Open `index.html` directly in a browser
(`file://` works) — no server, no build step, no dependencies, no CDN.

## Edit the list

Open `index.html`, scroll to the `SERVICES` array at the top of the `<script>`
block and change the entries:

```js
const SERVICES = [
  // Core
  { name: "Jira",             url: "https://jira.onecell.ru" },
  { name: "Keycloak",         url: "https://keycloak.onecell.ru" },
  { name: "ID (.ru)",         url: "https://id.onecell.ru" },
  { name: "ID (.ai)",         url: "https://id.onecell.ai" },
  { name: "Mattermost (.ru)", url: "https://mattermost.onecell.ru" },
  { name: "Mattermost (.ai)", url: "https://mattermost.onecell.ai" },
  // Important for work
  { name: "Meet",             url: "https://meet.onecell.ru" },
  { name: "Meet auth",        url: "https://auth-meet.onecell.ru" },
  { name: "Harbor",           url: "https://harbor.onecell.ru" },
  { name: "GitLab",            url: "https://gitlab.onecell.ru" },
  { name: "Docker registry",  url: "https://docker.onecell.ru" },
  { name: "Terraform",        url: "https://terraform.onecell.ru" },
  { name: "Portal",           url: "https://portal.onecell.ru" },
  { name: "Drive",            url: "https://drive.onecell.ru" },
  { name: "Cloud (.ai)",      url: "https://cloud.onecell.ai" },
  { name: "Dashboard (.ai)",  url: "https://dashboard.onecell.ai" },
  { name: "onecell.ru",       url: "https://onecell.ru" },
  { name: "onecell.ai",       url: "https://onecell.ai" },
  { name: "www.onecell.ai",   url: "https://www.onecell.ai" }
];
```

One object per service — `name` (label) and `url` (probed URL). Nothing else
needs to change.

## What it does

* On load, probes every entry and renders a dashboard (name, URL, status badge,
  latency, "checked Xs ago").
* Probe: `fetch(url, { mode: "no-cors", cache: "no-store", redirect: "follow" })`
  with a ~6s timeout (AbortController + setTimeout, with an `AbortSignal.timeout`
  feature-detect). Resolved = UP, rejected = DOWN.
* Fallback: if `fetch` is unavailable/blocked immediately, an `<img>` probe is
  used instead.
* Auto-refreshes every 30s with a visible countdown, plus a **Check now** button.

## Honest limitation

This is a browser-side reachability check. CORS hides HTTP status codes, so
**"UP" means the host answered at the network level** — not that the service is
healthy. A host that answers with an error page still shows as UP.
