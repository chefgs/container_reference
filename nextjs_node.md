# Learning Module: Next.js Containerization

## What You Will Learn

- How to containerize a Next.js application with multi-stage builds
- How to structure runtime images for production
- How to reduce image size using standalone output
- How to apply secure defaults in runtime

## Recommended Dockerfile (Next.js)

```dockerfile
FROM node:18-alpine AS base
WORKDIR /app
RUN npm install -g pnpm

FROM base AS deps
RUN apk add --no-cache libc6-compat
COPY package.json pnpm-lock.yaml* ./
RUN pnpm install --frozen-lockfile

FROM base AS builder
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN pnpm build

FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder /app/next.config.js ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./

USER nextjs
EXPOSE 3000
CMD ["node", "server.js"]
```

## Required Next.js Setting

Enable standalone output in `next.config.js`:

```js
const nextConfig = {
  output: 'standalone',
}

module.exports = nextConfig
```

## `.dockerignore` Baseline

```text
node_modules
.next/cache
.git
.github
.vscode
*.md
.env*
```

## Practice Exercises

1. Build and run the Next.js image.
2. Compare image size with/without standalone output.
3. Confirm app runs on port `3000`.
4. Confirm container process runs as non-root.

## Success Checklist

- [ ] Build uses lockfile (`--frozen-lockfile`)
- [ ] Runtime contains only production artifacts
- [ ] Telemetry is disabled in container
- [ ] Container starts as non-root user
