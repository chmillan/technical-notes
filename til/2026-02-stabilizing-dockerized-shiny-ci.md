# TIL — Stabilizing CI Builds for Dockerized Shiny Apps with Private Dependencies


**Date:** 2026-02-03

## Context
While enabling CI builds for a Dockerized Shiny application, I needed to support feature-branch images, private Git dependencies, and faster rebuilds — without merging into the default branch.

---

## What Broke
- Docker images could only be built from the default branch.
- CI builds failed during `renv::restore()` despite a valid lockfile.
- Dependency resolution behaved differently in CI (Linux) compared to local development.
- Build times were slow due to missing Docker layer caching.

---

## What I Changed
- Enabled branch-based Docker builds with tags `<branch>` and `<branch>-<sha>`.
- Introduced Docker Buildx with registry-backed cache.
- Used BuildKit secrets to securely access private Git repositories during builds.
- Disabled `pak` **only in CI** to prevent dependency solver conflicts, while keeping the lockfile unchanged.
- Centralized CI logic in Makefile targets to align local and CI workflows.

---

## Outcome
- CI builds became stable, reproducible, and significantly faster.
- Images can now be built from any branch and deployed manually to Kubernetes environments.
- Private dependencies are handled securely with no credential leakage.

---

## Takeaway
Reproducible builds depend not only on lockfiles, but also on how tooling behaves across environments.  
Selective simplification in CI can be the most reliable solution.

