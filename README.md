# Archivematica-k8s

Helm charts to deploy [Archivematica](https://www.archivematica.org/) on a Kubernetes cluster.

> This setup makes use of some CERN specific technology (EOS volume mounts, SSO) and it's designed for a k8s cluster orchestrated by OpenShift. You may need some modification to make this work on a different stack (e.g. minikube).

We target **Archivematica 1.13.x** and **Archivematica Storage Service 0.19.x**.

## Usage

[Helm](https://helm.sh) must be installed to use the charts.
Please refer to Helm's [documentation](https://helm.sh/docs/) to get started.

## Docker images

You shouldn't need anything different here, as the provided `values.yaml` is set to use the docker images we build and push to our [container registry](https://gitlab.cern.ch/digitalmemory/archivematica-helm/container_registry).

Archivematica images are unmodified versions from the upstream repositories, which are copied here as git submodules (check the `src` folders) and pinned on our target versions.

More images are used (e.g. MySQL, Elastic, ..). For those, we use standard images published on canonical registries.

### Build

If you wish to build them yourself, here's a quick overview:

| Service name                  | Dockerfile                                   | Context                            |
| ----------------------------- | -------------------------------------------- | ---------------------------------- |
| archivematica-mcp-server      | src/archivematica/src/MCPServer.Dockerfile   | src/archivematica/src/             |
| archivematica-mcp-client      | src/archivematica/src/MCPClient.Dockerfile   | src/archivematica/src/             |
| archivematica-dashboard       | src/archivematica/src/dashboard.Dockerfile   | src/archivematica/src/             |
| archivematica-storage-service | src/archivematica-storage-service/Dockerfile | src/archivematica-storage-service/ |
| am-clamav                     | images/clamav/Dockerfile                     | images/clamav                      |

In this case, replace the `image` values in the `values.yaml`.

## OpenShift project

Create an OpenShift project.

## Configuration

Most of the configuration values can be set in `values.yaml`. Depending on your deployment, you will need to change the `hostname` for which we'll create a route and `user_uid` under which the processes will run (note: ranges for allowed `user_uid`s are per-project on CERN OpenShift).

### Secrets

Some other values need to be set as secrets in the OpenShift project:

```
oc create secret generic \
  --from-literal="MYSQL_ROOT_PASSWORD=12345" \
  archivematica-settings
```

## References

- [PiqlConnect-AM](https://github.com/piql/PiqlConnect-AM)
