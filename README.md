# Traefik

version: '3.7'

services:
  fabiancdng-website:
    image: registry/.../my-website-image:latest
    labels:
      - traefik.enable=true
      - traefik.http.routers.my-website-frontend-http.rule=Host(`fabiancdng.com`) || Host(`www.fabiancdng.com`)
      - traefik.http.routers.my-website-frontend-http.entrypoints=http
      - traefik.http.routers.my-website-frontend-http.middlewares=redirect-to-https
      - traefik.http.routers.my-website-frontend-https.rule=Host(`fabiancdng.com`) || Host(`www.fabiancdng.com`)
      - traefik.http.routers.my-website-frontend-https.entrypoints=https
      - traefik.http.routers.my-website-frontend-https.tls=true
      - traefik.http.routers.my-website-frontend-https.tls.certresolver=letsencrypt
      - traefik.http.routers.my-website-frontend-https.middlewares=redirect-to-non-www
      - traefik.http.services.my-website-frontend.loadbalancer.server.port=3000
      - traefik.http.middlewares.redirect-to-https.redirectscheme.scheme=https
      - traefik.http.middlewares.redirect-to-https.redirectscheme.permanent=true
      - traefik.http.middlewares.redirect-to-non-www.redirectregex.regex=^https?://www.fabiancdng.com/(.*)
      - traefik.http.middlewares.redirect-to-non-www.redirectregex.replacement=https://fabiancdng.com/$${1}
      - traefik.http.middlewares.redirect-to-non-www.redirectregex.permanent=true
    networks:
      - proxy

networks:
  proxy:
    external: true
