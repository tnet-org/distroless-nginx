# NGINX
[![Docker Images](https://github.com/tnetmoe/distroless-nginx/actions/workflows/docker.yml/badge.svg)](https://github.com/tnetmoe/distroless-nginx/actions/workflows/docker.yml) [![Scheduled Docker Image Vulnerability Scan](https://github.com/tnetmoe/distroless-nginx/actions/workflows/vulnerability-scan.yml/badge.svg)](https://github.com/tnetmoe/distroless-nginx/actions/workflows/vulnerability-scan.yml)

Secure and lightweight [stable/mainline](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/#choosing-between-a-stable-or-a-mainline-version) [NGINX](http://nginx.org/) images based on [distroless](https://github.com/GoogleContainerTools/distroless).

## Key features
- **Minimal attack surface:** [Distroless](https://github.com/GoogleContainerTools/distroless) as base image with no package manager or shell.
- **Limited permissions:** Runs as a [non-root user](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html#rule-2-set-a-user) with [dropped capabilities](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html#rule-3-limit-capabilities-grant-only-specific-capabilities-needed-by-a-container) and [disabled privilege escalation](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html#rule-4-prevent-in-container-privilege-escalation).
- **Read-Only Filesystem:** Ensuring immutability, with writable volumes only where necessary.
- **Web Application Firewall**: WAF rules via preinstalled [ModSecurity](https://github.com/owasp-modsecurity/ModSecurity-nginx).
- **GeoIP2**: Compiled with the [GeoIP2 module](https://github.com/leev/ngx_http_geoip2_module) in addition to the default GeoIP1 module ([What's New in GeoIP2](https://dev.maxmind.com/geoip/whats-new-in-geoip2/)).

While it is possible to use the image for production, feel free to further optimize the images for your use-case to decrease possible attack surfaces (*wink wink nudge nudge*).

Note that debugging the images in production is straight up impossible, so you shouldn't use this image if you prefer simplicity and ease of use (open an issue/a pr if there are improvements in that regard).

## Getting started

### Available tags:
- `stable` (1.26.3)
  - `stable-modsecurity`
- `mainline` (1.27.4)
  - `mainline-modsecurity`

[stable vs mainline](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/#choosing-between-a-stable-or-a-mainline-version)

### Registry
- [GitHub Registry (`ghcr.io/tnetmoe/distroless-nginx`)](https://github.com/tnetmoe/distroless-nginx)
- ~~GitLab Registry~~ TODO
- ~~Docker Hub~~ no free plan
