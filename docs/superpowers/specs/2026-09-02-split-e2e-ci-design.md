# Split End-to-End CI Design

## Goal

Prevent Playwright browser setup from exhausting the main verification job's time budget while preserving every existing CI check.

## Job structure

The current `verify` job keeps PostgreSQL-backed unit and integration tests plus schema, OpenAPI, conformance, audit, and package-artifact verification.

A new `e2e` job runs independently with:

- the same pinned checkout, pnpm, Node.js, and PostgreSQL configuration;
- a frozen dependency install;
- Chromium installation with operating-system dependencies;
- the existing desktop and mobile Playwright suite;
- a 35-minute timeout.

The `containers` job depends on both `verify` and `e2e`, so container publication checks cannot proceed unless both paths succeed.

## Security and reproducibility

All action versions remain pinned. Checkout credentials are not persisted. Existing dependency review, production audit, schema, OpenAPI, conformance, package installation, container build, and smoke-test behavior is retained.

## Verification

The workflow file must parse successfully. The repository's local non-container checks and Playwright suite are run before publishing. After the PR is opened, the GitHub Actions run is the authoritative verification for the PostgreSQL service and Ubuntu browser dependency installation.
