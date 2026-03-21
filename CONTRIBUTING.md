# Contributing to status

This document describes the architecture, purpose, and standards for this repository. The AI PR reviewer reads this file — keep it accurate.

---

## Architecture

### Overview

This repository is a GitHub-native uptime monitor and status page powered by [Upptime](https://upptime.js.org). There is no application code — the "application" is a set of GitHub Actions workflows that periodically check configured URLs, record results as YAML files in `history/`, generate graphs, and publish a static status site via GitHub Pages.

| Component | Location | Purpose |
|---|---|---|
| Uptime checks | `.github/workflows/uptime.yml` | Scheduled pings to monitored URLs |
| Response time recording | `.github/workflows/response-time.yml` | Stores response-time data under `api/` |
| Graph generation | `.github/workflows/graphs.yml` | Renders PNG graphs into `graphs/` |
| Static site build | `.github/workflows/site.yml` | Builds and deploys the Upptime status page |
| Summary update | `.github/workflows/summary.yml` | Keeps `README.md` live-status badge in sync |
| Updates | `.github/workflows/updates.yml` | Keeps Upptime dependencies up to date |

### Data storage

All monitoring data is committed directly to the repository:

- `history/{service}.yml` — per-service incident and uptime history
- `api/{service}/response-time*.json` — response-time data for badge generation
- `graphs/{service}/response-time*.png` — pre-rendered response time graphs

Do not manually edit files in `history/`, `api/`, or `graphs/` — they are managed exclusively by the CI workflows.

### Configuration

Monitored services and Upptime settings are defined in `.upptimerc.yml` (if present) or within the individual workflow files. To add or remove a monitored URL, update the relevant workflow configuration.

### Authentication & Authorization

No application authentication. The workflows use the default `GITHUB_TOKEN` (with write access for committing history data) and optionally a `GH_PAT` secret for creating issues on downtime events.

---

## Coding Standards

### Adding a monitored URL

1. Add the URL entry to the Upptime configuration (`.upptimerc.yml` or workflow `with:` block).
2. Open a PR targeting `main`.
3. The CI workflows will begin collecting data after the PR is merged.

### Naming conventions

- Service slugs in workflow configuration should be lowercase with hyphens (e.g., `login-page`, `commercial-site`).
- Corresponding files in `history/`, `api/`, and `graphs/` use the same slug.

### Do not

- Manually commit to `history/`, `api/`, or `graphs/` — automated workflows own these directories.
- Pin workflow actions to SHA without also updating the Upptime config version.

---

## Pull Request Guidelines

- Target the `main` branch for all configuration changes.
- Every PR must pass the existing workflow checks with 0 errors.
- Keep PRs focused — one concern per PR (e.g., add a URL, update a schedule, fix a workflow).
- PR title should be imperative and concise (e.g., `Add API health check endpoint`).
- The AI reviewer will APPROVE or REQUEST_CHANGES automatically. Address all `critical` and `warning` severity issues before requesting human review.
