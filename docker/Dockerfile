# ──────────────────────────────────────────────────────────────
# Stage 1: Flowise + Playwright auf Debian-Bookworm
# ──────────────────────────────────────────────────────────────
FROM node:20-bookworm AS build
WORKDIR /usr/src/app

# 1) Flowise & Playwright installieren
RUN npm install -g flowise playwright@latest

# 2) Browser + Dependencies ziehen
RUN npx playwright install --with-deps

# ──────────────────────────────────────────────────────────────
# Stage 2: Runtime mit glibc-basierten Dependencies
# ──────────────────────────────────────────────────────────────
FROM node:20-bookworm
WORKDIR /home/node/app
USER node

# 1) Copy globale Module aus dem Build-Stage
COPY --from=build /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=build /usr/local/bin          /usr/local/bin

# 2) Port freigeben
EXPOSE 3000

# 3) Flowise starten
ENTRYPOINT ["flowise", "start"]
