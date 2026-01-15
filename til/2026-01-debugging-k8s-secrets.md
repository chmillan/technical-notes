# TIL — Debugging APIs with Kubernetes Secrets

## Context  
While deploying a backend API locally and in Kubernetes, I noticed inconsistent behavior when connecting to the PostgreSQL database. The same codebase worked in the cluster but failed during local development.

## What happened  
Locally, the API failed to connect to the database due to missing credentials. However, in Kubernetes the API started normally and all endpoints worked, even though no database password was explicitly defined in the code or local environment configuration.

This was confusing at first because the application appeared to be “missing” critical configuration.

## What I learned  
- Kubernetes injects sensitive configuration (such as database credentials) at runtime using **Secrets**, not through the code or repository.  
- Environment variables provided by the execution environment can fully override local defaults, making an application appear misconfigured when inspected only from the codebase.  
- A successful local build or container startup does not guarantee parity with the production runtime environment.

## Takeaway  
**An application’s behavior is defined as much by its runtime environment as by its code.**
