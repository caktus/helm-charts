# ODK Central

[ODK Central](https://github.com/caktus/central/) is the [ODK](https://getodk.org/) server. It manages user accounts and permissions, stores Form definitions, and allows data collection clients like ODK Collect to connect to it for Form download and Submission upload.

## TL;DR

```bash
helm repo add caktus https://caktus.github.io/helm-charts
helm install <release-name> caktus/odk-central
```

## Introduction

This chart deploys the Caktus fork of ODK Central and all its dependencies. This includes the following external dependencies:

- [Redis HA](https://github.com/DandyDeveloper/charts/tree/master/charts/redis-ha)
- [pyxform-http](https://github.com/getodk/pyxform-http/pkgs/container/pyxform-http)

It also installs these dependencies using images built by the Caktus fork of the ODK Central repository:

- [ODK Central backend](https://github.com/caktus/central/pkgs/container/central-service)
- [ODK Central frontend](https://github.com/caktus/central/pkgs/container/central-nginx)
- [Enketo](https://github.com/caktus/central/pkgs/container/central-enketo)

By default, the PostgreSQL database for the project is assumed to be an external service, but it can be installed as a subchart.
The database can be configured using environment variables.

## Prerequisites

* Kubernetes 1.28+

## Installing the Chart

To install the chart

```bash
helm repo add caktus https://caktus.github.io/helm-charts
helm install <release-name> caktus/odk-central
```

## Uninstalling the Chart

To uninstall/delete the deployment:

```bash
helm delete <release-name>
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the ODK Central chart and their default values.
Some of the paramaters are further documented with comments in the `values.yaml` file.

| Parameter                       | Description                                                               | Default                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------------|:--------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `global.centralDomain`          | The domain name for the ODK Central deployment.                           | `"www.example.com"`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `global.enketoSecretName`       | The name of the Secret to be created for Enketo secrets.                  | `"central-enketo-secrets"`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| `global.enketoSecrets`          | Enketo API keys and secrets.                                              | `{}`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `enketo.enabled`                | Whether Enketo is enabled.                                                | `true`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `enketo-redis-main`             | See https://github.com/DandyDeveloper/charts/tree/master/charts/redis-ha. | <pre>{<br>    "enabled": true,<br>    "replicas": 3,<br>    "haproxy": {<br>        "enabled": true,<br>        "replicas": 3<br>    },<br>    "persistentVolume": {<br>        "enabled": false<br>    },<br>    "emptyDir": {<br>        "sizeLimit": "500Mi"<br>    }<br>}</pre>                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `enketo-redis-cache`            | See https://github.com/DandyDeveloper/charts/tree/master/charts/redis-ha. | <pre>{<br>    "enabled": true,<br>    "replicas": 3,<br>    "haproxy": {<br>        "enabled": true,<br>        "replicas": 3<br>    },<br>    "persistentVolume": {<br>        "enabled": false<br>    },<br>    "emptyDir": {<br>        "sizeLimit": "500Mi"<br>    },<br>    "environmentVariables": null<br>}</pre>                                                                                                                                                                                                                                                                                                                                                                                                                                |
| `frontend.enabled`              | Whether the ODK Central frontend is enabled.                              | `true`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `frontend.environmentVariables` | Environment variables for the ODK Central frontend.                       | <pre>{<br>    "CERTBOT_EMAIL": "",<br>    "DOMAIN": "",<br>    "SENTRY_KEY": "3cf75f54983e473da6bd07daddf0d2ee",<br>    "SENTRY_ORG_SUBDOMAIN": "o130137",<br>    "SENTRY_PROJECT": "1298632",<br>    "SSL_TYPE": "upstream"<br>}</pre>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `backend.enabled`               | Whether the ODK Central backend is enabled.                               | `true`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `backend.environmentVariables`  | Environment variables for the ODK Central backend.                        | <pre>{<br>    "DB_HOST": "postgres14",<br>    "DB_NAME": "odk",<br>    "DB_PASSWORD": "odk",<br>    "DB_SSL": "null",<br>    "DB_USER": "odk",<br>    "DOMAIN": "",<br>    "EMAIL_FROM": "no-reply@",<br>    "EMAIL_HOST": "mail",<br>    "EMAIL_IGNORE_TLS": "true",<br>    "EMAIL_PASSWORD": "",<br>    "EMAIL_PORT": "25",<br>    "EMAIL_SECURE": "false",<br>    "EMAIL_USER": "",<br>    "HTTPS_PORT": "443",<br>    "NODE_OPTIONS": "",<br>    "OIDC_CLIENT_ID": "",<br>    "OIDC_CLIENT_SECRET": "",<br>    "OIDC_ENABLED": "false",<br>    "OIDC_ISSUER_URL": "",<br>    "SENTRY_KEY": "3cf75f54983e473da6bd07daddf0d2ee",<br>    "SENTRY_ORG_SUBDOMAIN": "o130137",<br>    "SENTRY_PROJECT": "1298632",<br>    "SYSADMIN_EMAIL": ""<br>}</pre> |
| `pyxform.enabled`               | Whether the pyxform service is enabled.                                   | `true`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |

Each of the subcharts (in the `charts` directory) has its own `values.yaml` file with additional defaults for the subchart.

To override the defaults, specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
helm repo add caktus https://caktus.github.io/helm-charts
helm install <release-name> \
  --set global.centralDomain=example.net \
    caktus/odk-central
```
