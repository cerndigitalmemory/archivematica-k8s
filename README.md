# Archivematica-k8s

Helm chart to deploy [Archivematica](https://www.archivematica.org/) on a Kubernetes cluster.

> This setup makes use of some CERN specific technology (EOS volume mounts, SSO) and it's designed for a k8s cluster orchestrated by OpenShift. You may need some modification to make this work on a different stack (e.g. minikube).

We target **Archivematica 1.13.x** and **Archivematica Storage Service 0.19.x**.

## Docker images

You shouldn't need anything different here, as the provided `values.yaml` uses the docker images we build and push to our [container registry](https://gitlab.cern.ch/digitalmemory/archivematica-helm/container_registry). They are unmodified versions from the upstream repositories, which are copied here as git submodules (check the `src` folders) and pinned on our target versions.

More images are used (e.g. MySQL, Elastic, ..). For those, we use standard images published on canonical registries.

### Build

If you wish to build them yourself, here's a quick overview:

| Service name                  | Docker file                                  | Context                            |
| ----------------------------- | -------------------------------------------- | ---------------------------------- |
| archivematica-mcp-server      | src/archivematica/src/MCPServer.Dockerfile   | src/archivematica/src/             |
| archivematica-mcp-client      | src/archivematica/src/MCPClient.Dockerfile   | src/archivematica/src/             |
| archivematica-dashboard       | src/archivematica/src/dashboard.Dockerfile   | src/archivematica/src/             |
| archivematica-storage-service | src/archivematica-storage-service/Dockerfile | src/archivematica-storage-service/ |

In this case, replace the `image` values in the `values.yaml`.

## Secrets

TODO

## Configuration

TODO

## References

- [PiqlConnect-AM](https://github.com/piql/PiqlConnect-AM)
