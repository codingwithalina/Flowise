# ──────────────────────────────────────────────────────────────
# Stage 1: Build Flowise + Playwright
# ──────────────────────────────────────────────────────────────
FROM node:20-alpine AS build
WORKDIR /usr/src/app
USER root

# Wenn du Puppeteer weiter nutzen willst, sonst ENV entfernen
ENV PUPPETEER_SKIP_DOWNLOAD=true

# 1) Flowise & Playwright global installieren
RUN npm install -g flowise playwright@latest

# 2) Browser-Binaries + native Dependencies ziehen
RUN npx playwright install --with-deps

# ──────────────────────────────────────────────────────────────
# Stage 2: Runtime mit allen System-Bibliotheken
# ──────────────────────────────────────────────────────────────
FROM node:20-alpine
USER root

# 1) System-Pakete für Chromium, Firefox, WebKit und Build-Tools
RUN apk add --no-cache \
      chromium \
      nss \
      freetype \
      harfbuzz \
      ca-certificates \
      ttf-freefont \
      git \
      python3 \
      py3-pip \
      make \
      g++ \
      build-base \
      cairo-dev \
      pango-dev \
      curl

# 2) Globale Node-Module & CLI aus dem Build-Stage übernehmen
COPY --from=build /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=build /usr/local/bin /usr/local/bin

# 3) (Optional) Wenn du Puppeteer nutzt
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser

# 4) Arbeitsverzeichnis & Port
WORKDIR /home/node/app
EXPOSE 3000

# 5) Flowise starten
ENTRYPOINT ["flowise", "start"]
