# ──────────────────────────────────────────────────────────────
# Stage 1: Flowise + Playwright (Build)
# ──────────────────────────────────────────────────────────────
FROM node:20-bookworm AS build
WORKDIR /usr/src/app

# Flowise + Playwright installieren
RUN npm install -g flowise playwright@latest

# Browser und system-spezifische Playwright-Deps herunterladen
RUN npx playwright install --with-deps    # Browser in /root/.cache/ms-playwright

# ──────────────────────────────────────────────────────────────
# Stage 2: Runtime
# ──────────────────────────────────────────────────────────────
FROM node:20-bookworm
WORKDIR /home/node/app
USER root

# 1) **Alle** System-Libraries installieren (Playwright-Shortcut)
### geändert: eigener apt-Block überflüssig → install-deps
RUN apt-get update \
 && npx playwright install-deps \
 && rm -rf /var/lib/apt/lists/*

# 2) Globale Node-Module + CLI übernehmen
COPY --from=build /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=build /usr/local/bin            /usr/local/bin

# 3) Browser-Cache übernehmen
### geändert: Pfad kopieren & HOME/ENV setzen
COPY --from=build /root/.cache/ms-playwright /home/pwuser/.cache/ms-playwright
ENV  PLAYWRIGHT_BROWSERS_PATH=/home/pwuser/.cache/ms-playwright
ENV  HOME=/home/pwuser

# 4) Benutzer anlegen + Rechte
RUN useradd -m pwuser \
 && chown -R pwuser:pwuser /home/pwuser/.cache

USER pwuser
WORKDIR /home/pwuser
EXPOSE 3000

ENTRYPOINT ["flowise", "start"]
