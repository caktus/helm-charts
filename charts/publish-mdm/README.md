# Publish MDM

[Publish MDM](https://github.com/caktus/publish-mdm/) is a proof-of-concept open-source tool to publish XLSForm templates from Google Sheets to [ODK Central](https://docs.getodk.org/).

## TL;DR

```bash
helm repo add caktus https://caktus.github.io/helm-charts
helm install <release-name> caktus/publish-mdm
```

## Introduction

This chart deploys the Publish MDM Django project. By default, the PostgreSQL database
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
helm install <release-name> caktus/publish-mdm
```

## Uninstalling the Chart

To uninstall/delete the deployment:

```bash
helm delete <release-name>
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the Publish MDM chart and their default values.
Some of the paramaters are further documented with comments in the `values.yaml` file.

If `asgi.enabled` is `false`, the chart will create only one deployment for both WSGI and ASGI, using a Daphne web server.
Else, it will create a Gunicorn-based WSGI deployment and a Daphne-based ASGI deployment.
The `asgi.*` parameters below are similar to their `publish-mdm` counterparts, but will only apply to the
ASGI deployment (when separate deployments are enabled).

If `infisical.enabled` is set, the [Infisical Helm chart][infisical] will be installed as a dependecy.
This requires a Kubernetes Secret named `infisical-secrets` to be created in the namespace
where you're installing Publish MDM, with the environment variables for the Infisical deployment.
For more details on `infisical-secrets` and Infisical's Helm chart values, refer to the [Infisical Helm chart documentation][infisical].

[infisical]: https://infisical.com/docs/self-hosting/deployment-options/kubernetes-helm

| Parameter                                                | Description                                            | Default                                                                                                                                                                                        |
|:---------------------------------------------------------|:-------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `publish-mdm.publishDomain`                              | The domain name for the Publish MDM deployment.        | `"www.example.com"`                                                                                                                                                                            |
| `publish-mdm.environmentVariables`                       | Environment variables for the deployment.              | `{"DATABASE_URL": "postgresql://postgres@172.17.0.1:9062/publish_mdm"}`                                                                                                                        |
| `publish-mdm.replicaCount`                               | Number of replicas for the deployment.                 | `1`                                                                                                                                                                                            |
| `publish-mdm.image.repository`                           | Repository for the Publish MDM image.                  | `"ghcr.io/caktus/publish-mdm"`                                                                                                                                                                 |
| `publish-mdm.image.pullPolicy`                           | Image pull policy.                                     | `"IfNotPresent"`                                                                                                                                                                               |
| `publish-mdm.image.tag`                                  | Publish MDM image tag.                                 | `"latest"`                                                                                                                                                                                     |
| `publish-mdm.imagePullSecrets`                           | Pull secrets for the image repository.                 | `[]`                                                                                                                                                                                           |
| `publish-mdm.nameOverride`                               | Release name override.                                 | `""`                                                                                                                                                                                           |
| `publish-mdm.fullnameOverride`                           | Full name override for release.                        | `""`                                                                                                                                                                                           |
| `publish-mdm.serviceAccount.create`                      | Specifies whether a ServiceAccount should be created.  | `true`                                                                                                                                                                                         |
| `publish-mdm.serviceAccount.automount`                   | Whether to automount the ServiceAccount.               | `true`                                                                                                                                                                                         |
| `publish-mdm.serviceAccount.annotations`                 | ServiceAccount annotations.                            | `{}`                                                                                                                                                                                           |
| `publish-mdm.serviceAccount.name`                        | The name of the ServiceAccount to create.              | `""`                                                                                                                                                                                           |
| `publish-mdm.podAnnotations`                             | Pod annotations.                                       | `{}`                                                                                                                                                                                           |
| `publish-mdm.podLabels`                                  | Pod labels.                                            | `{}`                                                                                                                                                                                           |
| `publish-mdm.podSecurityContext`                         | Pod security context.                                  | `{}`                                                                                                                                                                                           |
| `publish-mdm.securityContext`                            | Security context for the deployment.                   | `{}`                                                                                                                                                                                           |
| `publish-mdm.service.type`                               | Service Type.                                          | `"ClusterIP"`                                                                                                                                                                                  |
| `publish-mdm.service.port`                               | Kubernetes service port.                               | `8000`                                                                                                                                                                                         |
| `publish-mdm.ingress.enabled`                            | Whether to enable Ingress.                             | `true`                                                                                                                                                                                         |
| `publish-mdm.ingress.className`                          | Ingress class.                                         | `"nginx"`                                                                                                                                                                                      |
| `publish-mdm.ingress.annotations`                        | Ingress annotations.                                   | `{}`                                                                                                                                                                                           |
| `publish-mdm.ingress.hosts`                              | Ingress hosts.                                         | <pre>[<br>  {<br>    "host": "chart-example.local",<br>    "paths": [<br>      {<br>        "path": "/",<br>        "pathType": "ImplementationSpecific"<br>      }<br>    ]<br>  }<br>]</pre> |
| `publish-mdm.ingress.tls`                                | Ingress TLS hosts.                                     | `[]`                                                                                                                                                                                           |
| `publish-mdm.ingressApp.enabled`                         | Whether to enable a second Ingress.                    | `false`                                                                                                                                                                                        |
| `publish-mdm.ingressApp.className`                       | Ingress class for second Ingress.                      | `"nginx"`                                                                                                                                                                                      |
| `publish-mdm.ingressApp.annotations`                     | Ingress annotations for second Ingress.                | `{}`                                                                                                                                                                                           |
| `publish-mdm.ingressApp.hosts`                           | Ingress hosts for second Ingress.                      | <pre>[<br>  {<br>    "host": "chart-example.local",<br>    "paths": [<br>      {<br>        "path": "/",<br>        "pathType": "ImplementationSpecific"<br>      }<br>    ]<br>  }<br>]</pre> |
| `publish-mdm.ingressApp.tls`                             | Ingress TLS hosts for second Ingress.                  | `[]`                                                                                                                                                                                           |
| `publish-mdm.resources`                                  | Kubernetes pod resources.                              | `{}`                                                                                                                                                                                           |
| `publish-mdm.autoscaling.enabled`                        | Whether to enable autoscaling.                         | `false`                                                                                                                                                                                        |
| `publish-mdm.autoscaling.minReplicas`                    | Minimum replicas for autoscaling.                      | `1`                                                                                                                                                                                            |
| `publish-mdm.autoscaling.maxReplicas`                    | Maximum replicas for autoscaling.                      | `100`                                                                                                                                                                                          |
| `publish-mdm.autoscaling.targetCPUUtilizationPercentage` | The target CPU utilization percentage for autoscaling. | `80`                                                                                                                                                                                           |
| `publish-mdm.volumes`                                    | Kubernetes volumes.                                    | `[]`                                                                                                                                                                                           |
| `publish-mdm.volumeMounts`                               | Kubernetes volumes mounts.                             | `[]`                                                                                                                                                                                           |
| `publish-mdm.nodeSelector`                               | Pod node selector.                                     | `{}`                                                                                                                                                                                           |
| `publish-mdm.tolerations`                                | Kubernetes tolerations.                                | `[]`                                                                                                                                                                                           |
| `publish-mdm.affinity`                                   | Affinity for the deployment.                           | `{}`                                                                                                                                                                                           |
| `asgi.enabled`                                           | Whether to set up separate WSGI and ASGI deployments.  | `false`                                                                                                                                                                                        |
| `asgi.publishDomain`                                     |                                                        | `"www.example.com"`                                                                                                                                                                            |
| `asgi.environmentVariables`                              |                                                        | `{"DATABASE_URL": "postgresql://postgres@172.17.0.1:9062/publish_mdm"}`                                                                                                                        |
| `asgi.replicaCount`                                      |                                                        | `1`                                                                                                                                                                                            |
| `asgi.image.repository`                                  |                                                        | `"ghcr.io/caktus/publish-mdm"`                                                                                                                                                                 |
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
| `infisical.enabled`                                      | Whether to install the Infisical subchart.             | `true`                                                                                                                                                                                         |

To override the defaults, specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
helm repo add caktus https://caktus.github.io/helm-charts
helm install <release-name> \
  --set publish-mdm.publishDomain=example.net \
    caktus/publish-mdm
```
