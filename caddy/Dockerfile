FROM caddy:builder AS builder

# Build Caddy with the Cloudflare DNS provider plugin using xcaddy
RUN xcaddy build \
    --with github.com/caddy-dns/cloudflare \
    --with github.com/WeidiDeng/caddy-cloudflare-ip

FROM caddy:latest
COPY --from=builder /usr/bin/caddy /usr/bin/caddy

CMD ["caddy", "run", "--config", "/etc/caddy/Caddyfile", "--watch","--adapter", "caddyfile"]