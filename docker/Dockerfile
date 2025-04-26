# ──────────────────────────────────────────────────────────────
# Stage 1: Flowise + Playwright (Build)
# ──────────────────────────────────────────────────────────────
FROM node:20-bookworm AS build
WORKDIR /usr/src/app
RUN npm install -g flowise playwright@latest
RUN npx playwright install --with-deps      # ⇒ legt Browser unter /root/.cache/ms-playwright ab

# ──────────────────────────────────────────────────────────────
# Stage 2: Runtime
# ──────────────────────────────────────────────────────────────
FROM node:20-bookworm
WORKDIR /home/node/app
USER root

# 1) System-Libs
RUN apt-get update && apt-get install -y \
      libnss3 libatk1.0-0 libatk-bridge2.0-0 \
      libx11-xcb1 libxcomposite1 libxdamage1 libxfixes3 \
      libpango-1.0-0 libpangocairo-1.0-0 \
      libcups2 libdrm2 libgbm1 libasound2 libgtk-3-0 \
      libxrandr2 libxshmfence1 libwayland-client0 \
      libwayland-cursor0 libwayland-egl1 libwayland-server0 \
   && rm -rf /var/lib/apt/lists/*

# 2) Globale Node-Module + CLI übernehmen
COPY --from=build /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=build /usr/local/bin            /usr/local/bin

# 2a) **Browser-Cache übernehmen (neu)**
COPY --from=build /root/.cache/ms-playwright /home/pwuser/.cache/ms-playwright
ENV  PLAYWRIGHT_BROWSERS_PATH=/home/pwuser/.cache/ms-playwright

# 2b) Berechtigungen anpassen (damit der Nicht-Root-User schreiben darf)
RUN useradd -m pwuser \
 && chown -R pwuser:pwuser /home/pwuser/.cache/ms-playwright

USER pwuser
WORKDIR /home/pwuser
EXPOSE 3000

ENTRYPOINT ["flowise", "start"]
