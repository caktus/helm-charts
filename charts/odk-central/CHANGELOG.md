# Changelog

This file documents all notable changes to the ODK Central Helm Chart.
The release numbering uses [semantic versioning](http://semver.org).

## 0.3.1

- Set the Host header for liveness and readiness checks for the ODK Central frontend. (#3)

## 0.3.0

- Add optional 2nd ingress to allow splitting traffic for public & VPN (#2)

## 0.2.0

- Update redis-ha subchart and enable haproxy (76abe4a48d3eecf0c76c5ad060235cf15a049497)

## 0.1.3

- Fix `ENKETO_URL` and `DOMAIN` environment variables (dc8a34f4aa46a5d3be448385360042bea4216052)

## 0.1.2

- Fix missing Enketo secrets (d2c8a86499f5ab6d234abd8be6c24e545ed96c0c)

## 0.1.1

- Build chart dependencies in CI (3c181959ad18513e9dd7d9f29366d960f67682cb)
- Remove extraneous template files (3a164ff69dc38797fdfb1e29ab408f25a3fdddb0)

## 0.1.0

- Initial release (#1)
