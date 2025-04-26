# ──────────────────────────────────────────────────────────────
# Stage 1: Flowise + Playwright auf Debian-Bookworm
# ──────────────────────────────────────────────────────────────
FROM node:20-bookworm AS build
WORKDIR /usr/src/app
USER root

# 1) Flowise & Playwright global installieren
RUN npm install -g flowise playwright@latest

# 2) Playwright-Browser + native Dependencies herunterladen
RUN npx playwright install --with-deps

# ──────────────────────────────────────────────────────────────
# Stage 2: Runtime mit glibc-basierten Browser-Abhängigkeiten
# ──────────────────────────────────────────────────────────────
FROM node:20-bookworm
WORKDIR /home/node/app
USER root

# 1) System-Libraries für alle Playwright-Browser nachinstallieren
RUN apt-get update \
 && apt-get install -y \
    libnss3 libatk1.0-0 libatk-bridge2.0-0 \
    libx11-xcb1 libxcomposite1 libxdamage1 libxfixes3 \
    libpango-1.0-0 libpangocairo-1.0-0 \
    libcups2 libdrm2 libgbm1 libasound2 libgtk-3-0 \
    libxrandr2 libxshmfence1 libwayland-client0 \
    libwayland-cursor0 libwayland-egl1 libwayland-server0 \
 && rm -rf /var/lib/apt/lists/*

# 2) Globale Node-Module & CLI aus dem Build-Stage übernehmen
COPY --from=build /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=build /usr/local/bin          /usr/local/bin

# 3) Nicht-root-User für Sicherheit (optional)
RUN useradd -m pwuser
USER pwuser

# 4) Arbeitsverzeichnis & Port
WORKDIR /home/pwuser
EXPOSE 3000

# 5) Flowise starten
ENTRYPOINT ["flowise", "start"]
