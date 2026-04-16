# Learning Module: Angular Frontend + Node.js Backend Containers

## What You Will Learn

- Why frontend and backend images are usually built separately
- How multi-stage builds reduce image size
- How to harden backend containers with non-root users
- How to keep build context clean with `.dockerignore`

## Suggested Project Layout

```text
project-root/
  frontend/
  backend/
```

## Frontend Dockerfile (Angular + Nginx)

```dockerfile
FROM node:18-alpine AS base
WORKDIR /app
RUN npm install -g pnpm

FROM base AS deps
COPY package.json pnpm-lock.yaml* ./
RUN pnpm install --frozen-lockfile

FROM deps AS build
COPY . .
ENV NODE_ENV=production
RUN pnpm run build

FROM nginx:alpine AS runtime
COPY --from=build /app/dist/angular-app /usr/share/nginx/html
EXPOSE 80
```

## Backend Dockerfile (Node.js API)

```dockerfile
FROM node:18-alpine AS base
WORKDIR /app
RUN npm install -g pnpm

FROM base AS deps
COPY package.json pnpm-lock.yaml* ./
RUN pnpm install --frozen-lockfile

FROM deps AS build
COPY . .
ENV NODE_ENV=production
RUN pnpm run build
RUN pnpm prune --prod

FROM node:18-alpine AS runtime
WORKDIR /app
ENV NODE_ENV=production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 appuser

COPY --from=build /app/package.json ./
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules

RUN chown -R appuser:nodejs /app
USER appuser

EXPOSE 3000
CMD ["node", "dist/main.js"]
```

## `.dockerignore` Baseline

Use this baseline in both frontend and backend projects:

```text
node_modules
.git
.github
.vscode
coverage
dist
*.md
.env*
```

## Practice Exercises

1. Build frontend and backend images independently.
2. Run both containers and confirm frontend can call backend.
3. Measure image sizes before/after dependency pruning.
4. Confirm backend runs as non-root.

## Success Checklist

- [ ] Builds are reproducible with lockfiles
- [ ] Runtime image excludes source-only files
- [ ] Containers start with expected ports
- [ ] Backend does not run as root
