# ModSecurity example

Docker compose example for the modsecurity image.

### Getting started
Check out the [docker-compose.yml](./docker-compose.yml) config.

# ModSecurity setup
1. Create your custom `modsecurity.conf`. You can use the [modsecurity.conf-recommended](https://github.com/owasp-modsecurity/ModSecurity/blob/v3/master/modsecurity.conf-recommended) template for that.
2. Create your rules.
   1. To get started, you can check out [OWASP CRS](https://github.com/coreruleset/coreruleset) ([INSTALL.md](https://github.com/coreruleset/coreruleset/blob/main/INSTALL.md)).
