# ODK Publish

[ODK Publish](https://github.com/caktus/odk-publish/) is a proof-of-concept open-source tool to publish XLSForm templates from Google Sheets to [ODK Central](https://docs.getodk.org/).

## TL;DR

```bash
helm repo add caktus https://caktus.github.io/helm-charts
helm install <release-name> caktus/odk-publish
```

## Introduction

This chart deploys the ODK Publish Django project. By default, the PostgreSQL database
for the project is assumed to be an external service, but it can be installed as a subchart.
The database can be configured using the `DATABASE_URL` environment variable.

The chart can set up separate deployments for WSGI (using Gunicorn) and ASGI (using Daphne), but
the default is one Daphne-based deployment for both WSGI and ASGI.

## Prerequisites

* Kubernetes 1.28+

## Installing the Chart

To install the chart

```bash
helm repo add caktus https://caktus.github.io/helm-charts
helm install <release-name> caktus/odk-publish
```

## Uninstalling the Chart

To uninstall/delete the deployment:

```bash
helm delete <release-name>
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the ODK Publish chart and their default values.
Some of the paramaters are further documented with comments in the `values.yaml` file.

If `asgi.enabled` is `false`, the chart will create only one deployment for both WSGI and ASGI, using a Daphne web server.
Else, it will create a Gunicorn-based WSGI deployment and a Daphne-based ASGI deployment.
The `asgi.*` parameters below are similar to their `odk-publish` counterparts, but will only apply to the
ASGI deployment (when separate deployments are enabled).

| Parameter                                                | Description                                            | Default                                                                                                                                                                                        |
|:---------------------------------------------------------|:-------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `odk-publish.publishDomain`                              | The domain name for the ODK Publish deployment.        | `"www.example.com"`                                                                                                                                                                            |
| `odk-publish.environmentVariables`                       | Environment variables for the deployment.              | `{"DATABASE_URL": "postgresql://postgres@172.17.0.1:9062/odk_publish"}`                                                                                                                        |
| `odk-publish.replicaCount`                               | Number of replicas for the deployment.                 | `1`                                                                                                                                                                                            |
| `odk-publish.image.repository`                           | Repository for the ODK Publish image.                  | `"ghcr.io/caktus/odk-publish"`                                                                                                                                                                 |
| `odk-publish.image.pullPolicy`                           | Image pull policy.                                     | `"IfNotPresent"`                                                                                                                                                                               |
| `odk-publish.image.tag`                                  | ODK Publish image tag.                                 | `"latest"`                                                                                                                                                                                     |
| `odk-publish.imagePullSecrets`                           | Pull secrets for the image repository.                 | `[]`                                                                                                                                                                                           |
| `odk-publish.nameOverride`                               | Release name override.                                 | `""`                                                                                                                                                                                           |
| `odk-publish.fullnameOverride`                           | Full name override for release.                        | `""`                                                                                                                                                                                           |
| `odk-publish.serviceAccount.create`                      | Specifies whether a ServiceAccount should be created.  | `true`                                                                                                                                                                                         |
| `odk-publish.serviceAccount.automount`                   | Whether to automount the ServiceAccount.               | `true`                                                                                                                                                                                         |
| `odk-publish.serviceAccount.annotations`                 | ServiceAccount annotations.                            | `{}`                                                                                                                                                                                           |
| `odk-publish.serviceAccount.name`                        | The name of the ServiceAccount to create.              | `""`                                                                                                                                                                                           |
| `odk-publish.podAnnotations`                             | Pod annotations.                                       | `{}`                                                                                                                                                                                           |
| `odk-publish.podLabels`                                  | Pod labels.                                            | `{}`                                                                                                                                                                                           |
| `odk-publish.podSecurityContext`                         | Pod security context.                                  | `{}`                                                                                                                                                                                           |
| `odk-publish.securityContext`                            | Security context for the deployment.                   | `{}`                                                                                                                                                                                           |
| `odk-publish.service.type`                               | Service Type.                                          | `"ClusterIP"`                                                                                                                                                                                  |
| `odk-publish.service.port`                               | Kubernetes service port.                               | `8000`                                                                                                                                                                                         |
| `odk-publish.ingress.enabled`                            | Whether to enable Ingress.                             | `true`                                                                                                                                                                                         |
| `odk-publish.ingress.className`                          | Ingress class.                                         | `"nginx"`                                                                                                                                                                                      |
| `odk-publish.ingress.annotations`                        | Ingress annotations.                                   | `{}`                                                                                                                                                                                           |
| `odk-publish.ingress.hosts`                              | Ingress hosts.                                         | <pre>[<br>  {<br>    "host": "chart-example.local",<br>    "paths": [<br>      {<br>        "path": "/",<br>        "pathType": "ImplementationSpecific"<br>      }<br>    ]<br>  }<br>]</pre> |
| `odk-publish.ingress.tls`                                | Ingress TLS hosts.                                     | `[]`                                                                                                                                                                                           |
| `odk-publish.ingressApp.enabled`                         | Whether to enable a second Ingress.                    | `false`                                                                                                                                                                                        |
| `odk-publish.ingressApp.className`                       | Ingress class for second Ingress.                      | `"nginx"`                                                                                                                                                                                      |
| `odk-publish.ingressApp.annotations`                     | Ingress annotations for second Ingress.                | `{}`                                                                                                                                                                                           |
| `odk-publish.ingressApp.hosts`                           | Ingress hosts for second Ingress.                      | <pre>[<br>  {<br>    "host": "chart-example.local",<br>    "paths": [<br>      {<br>        "path": "/",<br>        "pathType": "ImplementationSpecific"<br>      }<br>    ]<br>  }<br>]</pre> |
| `odk-publish.ingressApp.tls`                             | Ingress TLS hosts for second Ingress.                  | `[]`                                                                                                                                                                                           |
| `odk-publish.resources`                                  | Kubernetes pod resources.                              | `{}`                                                                                                                                                                                           |
| `odk-publish.autoscaling.enabled`                        | Whether to enable autoscaling.                         | `false`                                                                                                                                                                                        |
| `odk-publish.autoscaling.minReplicas`                    | Minimum replicas for autoscaling.                      | `1`                                                                                                                                                                                            |
| `odk-publish.autoscaling.maxReplicas`                    | Maximum replicas for autoscaling.                      | `100`                                                                                                                                                                                          |
| `odk-publish.autoscaling.targetCPUUtilizationPercentage` | The target CPU utilization percentage for autoscaling. | `80`                                                                                                                                                                                           |
| `odk-publish.volumes`                                    | Kubernetes volumes.                                    | `[]`                                                                                                                                                                                           |
| `odk-publish.volumeMounts`                               | Kubernetes volumes mounts.                             | `[]`                                                                                                                                                                                           |
| `odk-publish.nodeSelector`                               | Pod node selector.                                     | `{}`                                                                                                                                                                                           |
| `odk-publish.tolerations`                                | Kubernetes tolerations.                                | `[]`                                                                                                                                                                                           |
| `odk-publish.affinity`                                   | Affinity for the deployment.                           | `{}`                                                                                                                                                                                           |
| `asgi.enabled`                                           | Whether to set up separate WSGI and ASGI deployments.  | `false`                                                                                                                                                                                        |
| `asgi.publishDomain`                                     |                                                        | `"www.example.com"`                                                                                                                                                                            |
| `asgi.environmentVariables`                              |                                                        | `{"DATABASE_URL": "postgresql://postgres@172.17.0.1:9062/odk_publish"}`                                                                                                                        |
| `asgi.replicaCount`                                      |                                                        | `1`                                                                                                                                                                                            |
| `asgi.image.repository`                                  |                                                        | `"ghcr.io/caktus/odk-publish"`                                                                                                                                                                 |
| `asgi.image.pullPolicy`                                  |                                                        | `"IfNotPresent"`                                                                                                                                                                               |
| `asgi.image.tag`                                         |                                                        | `"latest"`                                                                                                                                                                                     |
| `asgi.imagePullSecrets`                                  |                                                        | `[]`                                                                                                                                                                                           |
| `asgi.nameOverride`                                      |                                                        | `""`                                                                                                                                                                                           |
| `asgi.fullnameOverride`                                  |                                                        | `""`                                                                                                                                                                                           |
| `asgi.serviceAccount.create`                             |                                                        | `true`                                                                                                                                                                                         |
| `asgi.serviceAccount.automount`                          |                                                        | `true`                                                                                                                                                                                         |
| `asgi.serviceAccount.annotations`                        |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.serviceAccount.name`                               |                                                        | `""`                                                                                                                                                                                           |
| `asgi.podAnnotations`                                    |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.podLabels`                                         |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.podSecurityContext`                                |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.securityContext`                                   |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.service.type`                                      |                                                        | `"ClusterIP"`                                                                                                                                                                                  |
| `asgi.service.port`                                      |                                                        | `8000`                                                                                                                                                                                         |
| `asgi.ingress.enabled`                                   |                                                        | `true`                                                                                                                                                                                         |
| `asgi.ingress.className`                                 |                                                        | `"nginx"`                                                                                                                                                                                      |
| `asgi.ingress.annotations`                               |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.ingress.hosts`                                     |                                                        | <pre>[<br>  {<br>    "host": "chart-example.local",<br>    "paths": [<br>      {<br>        "path": "/ws",<br>        "pathType": "Prefix"<br>      }<br>    ]<br>  }<br>]</pre>               |
| `asgi.ingress.tls`                                       |                                                        | `[]`                                                                                                                                                                                           |
| `asgi.ingressApp.enabled`                                |                                                        | `false`                                                                                                                                                                                        |
| `asgi.ingressApp.className`                              |                                                        | `"nginx"`                                                                                                                                                                                      |
| `asgi.ingressApp.annotations`                            |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.ingressApp.hosts`                                  |                                                        | <pre>[<br>  {<br>    "host": "chart-example.local",<br>    "paths": [<br>      {<br>        "path": "/",<br>        "pathType": "ImplementationSpecific"<br>      }<br>    ]<br>  }<br>]</pre> |
| `asgi.ingressApp.tls`                                    |                                                        | `[]`                                                                                                                                                                                           |
| `asgi.resources`                                         |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.autoscaling.enabled`                               |                                                        | `false`                                                                                                                                                                                        |
| `asgi.autoscaling.minReplicas`                           |                                                        | `1`                                                                                                                                                                                            |
| `asgi.autoscaling.maxReplicas`                           |                                                        | `100`                                                                                                                                                                                          |
| `asgi.autoscaling.targetCPUUtilizationPercentage`        |                                                        | `80`                                                                                                                                                                                           |
| `asgi.volumes`                                           |                                                        | `[]`                                                                                                                                                                                           |
| `asgi.volumeMounts`                                      |                                                        | `[]`                                                                                                                                                                                           |
| `asgi.nodeSelector`                                      |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.tolerations`                                       |                                                        | `[]`                                                                                                                                                                                           |
| `asgi.affinity`                                          |                                                        | `{}`                                                                                                                                                                                           |
| `asgi.healthChecksPath`                                  | The path for liveness and readiness checks.            | `"/ws/health/"`                                                                                                                                                                                |

To override the defaults, specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
helm repo add caktus https://caktus.github.io/helm-charts
helm install <release-name> \
  --set odk-publish.publishDomain=example.net \
    caktus/odk-publish
```
