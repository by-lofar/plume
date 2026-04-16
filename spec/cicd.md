# spec: CI/CD

> Scope: GitHub Actions pipeline, Dependabot configuration, and deployment trigger strategy.

---

## 1. Principles

- Every pull request runs the full test suite before merge
- Every commit on `main` — regardless of origin — runs the full test suite then deploys
- No deployment proceeds if any test fails
- Dependabot keeps dependencies up to date; its PRs are subject to the same pipeline as any other PR and auto-merge if tests pass

---

## 2. Triggers

### Pull requests

All pull requests targeting `main` trigger the `ci` workflow. This applies to Dependabot PRs and manual PRs alike.

### Push to main

Every push to `main` (including merges) triggers the `ci` workflow followed by the `deploy` workflow.

---

## 3. Workflows

### `ci.yml` — build, preview, test

Runs on: `pull_request` targeting `main`, `push` to `main`.

Steps in order:

1. **Checkout** — `actions/checkout`
2. **Setup Node.js** — version pinned in `.nvmrc` or `package.json#engines`
3. **Install dependencies** — `npm ci`
4. **Build** — `npm run build` (`astro build`)
5. **Start preview** — handled by Playwright `webServer` config, not a manual step
6. **Test: internal links** — `npx playwright test tests/links.spec.ts`
7. **Test: semantic structure** — `npx playwright test tests/structure.spec.ts`
8. **Test: RSS feed** — `npx playwright test tests/rss.spec.ts`
9. **Test: accessibility** — `npx playwright test tests/a11y.spec.ts`

All test steps run sequentially. A failure at any step aborts the workflow.

### `deploy.yml` — deployment

Runs on: `push` to `main`, after `ci` passes.

Steps:

1. **Trigger Coolify webhook** — HTTP call to the Coolify deployment webhook URL, stored as a GitHub Actions secret (`COOLIFY_WEBHOOK_URL`)

> Deploy job details are deferred — this workflow is a placeholder pending Coolify configuration.

---

## 4. Dependabot

**File:** `.github/dependabot.yml`

Dependabot monitors `npm` dependencies and opens pull requests for updates weekly.

```yaml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
    assignees:
      - "<github-username>"
    commit-message:
      prefix: "chore(deps)"
```

### Auto-merge

Dependabot PRs auto-merge when the `ci` workflow passes. This is handled via a dedicated workflow:

**File:** `.github/workflows/dependabot-automerge.yml`

Runs on: `pull_request` from `dependabot[bot]`, after `ci` passes.

```yaml
- uses: actions/github-script@v7
  with:
    script: |
      await github.rest.pulls.merge({
        owner: context.repo.owner,
        repo: context.repo.repo,
        pull_number: context.payload.pull_request.number,
        merge_method: 'squash'
      })
```

Auto-merge applies to all Dependabot PRs — patch, minor, and major. If a major update breaks the build or tests, the pipeline blocks the merge naturally.

---

## 5. Secrets

| Secret                 | Usage                          |
|------------------------|--------------------------------|
| `COOLIFY_WEBHOOK_URL`  | Coolify deployment trigger URL |

No other secrets are required for a static site with no environment-specific configuration.

---

## 6. Out of scope

- Staging environment or preview deployments per PR
- Manual approval gates before deployment
- Notifications (Slack, email) on failure