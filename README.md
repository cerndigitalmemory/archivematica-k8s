# Archivematica-k8s

Helm chart to deploy [Archivematica](https://www.archivematica.org/) on a Kubernetes cluster.

We target **Archivematica 1.13.x** and **Archivematica Storage Service 0.19.x**.

## Build Docker images

1. archivematica-mcp-server -> src/archivematica/src/MCPServer.Dockerfile
2. archivematica-mcp-client -> src/archivematica/src/MCPClient.Dockerfile
3. archivematica-dashboard -> src/archivematica/src/dashboard.Dockerfile
4. archivematica-storage-service -> src/archivematica-storage-service/Dockerfile

Check the [container registry](https://gitlab.cern.ch/digitalmemory/archivematica-helm/container_registry) to pull already built images.

## References

- [PiqlConnect-AM](https://github.com/piql/PiqlConnect-AM)
