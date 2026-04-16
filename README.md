# Container Learning Repository

This repository is a hands-on learning space for building, optimizing, and running containers for modern JavaScript applications.

## Learning Goals

By working through these guides, you will learn how to:

- Write production-ready multi-stage Dockerfiles
- Separate frontend and backend container concerns
- Reduce image size and improve build caching
- Run containers with safer defaults (non-root users, minimal runtime images)
- Use `.dockerignore` effectively

## Learning Path

1. **Angular + Node module**  
   Start here to learn separate frontend/backend containerization patterns:  
   `/home/runner/work/container_reference/container_reference/angular_node.md`

2. **Next.js + Node module**  
   Then learn optimized Next.js container builds and runtime patterns:  
   `/home/runner/work/container_reference/container_reference/nextjs_node.md`

## Prerequisites

- Docker installed locally
- Basic understanding of Node.js projects
- A sample app (or your own frontend/backend app) to practice with

## How to Use This Repository

- Read one module at a time
- Copy the Dockerfile examples into your app
- Build, run, and validate each container
- Apply the optimization and security checklist from each module

## Practice Routine

For each module:

1. Build the image
2. Run the container
3. Verify the app is reachable
4. Check image size and improve it
5. Run as non-root user
6. Repeat until reproducible

---

Use this repo as your personal container lab: learn by building, validating, and iterating.
