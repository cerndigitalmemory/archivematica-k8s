# Archivematica-k8s

Helm charts to deploy [Archivematica](https://www.archivematica.org/) on a Kubernetes cluster.

> No managed instances are used (MySQL pods are deployed and standard volumes are used for storage) so this should provide a quite generic setup.

We target **Archivematica 1.13.x** and **Archivematica Storage Service 0.19.x**.

## Usage

You need Helm, your K8s orchestrator cli (e.g. the `oc` cli tool for OpenShift) and optionally Docker if you want to build the images yourself.

1. Configure `values.yaml`
2. Configure secrets
3. Install the release through Helm:

```bash
git clone https://gitlab.cern.ch/digitalmemory/archivematica-helm
cd archivematica-helm
helm install <release-name> am-deploy --values=values.yaml
```

A more detailed walkthrough on how to create projects and deploy on OpenShift can be found [here](https://gitlab.cern.ch/digitalmemory/openshift-deploy).

## Configuration

Most of the configuration values can be set in `values.yaml`. Depending on your deployment, you will need to change the `hostname` for which we'll create a route and `user_uid` under which the processes will run (note: ranges for allowed `user_uid`s are per-project on CERN OpenShift).

| Value             | Description                                               |
| ----------------- | --------------------------------------------------------- |
| route/hostname    | Hostname for the Dashboard                                |
| route/hostname_ss | Hostname for the Storage Service                          |
| general/user_uid  | Pick this based on your OpenShift project's allowed range |

Example values for hostname on CERN Openshift are `<NAME>.web.cern.ch`.

### MySQL workaround

If the last init pod fails: open the mysql pod terminal and run `mysql -uroot -p` (login with the MySQL root password you set in the secrets), then execute this query:

```sql
CREATE USER 'root'@'%' IDENTIFIED BY '12345a'; GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '12345a'; GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION; FLUSH PRIVILEGES; CREATE DATABASE IF NOT EXISTS archivematica_db; CREATE USER 'archivematica'@'%' IDENTIFIED BY 'demo'; CREATE DATABASE IF NOT EXISTS MCP; GRANT ALL PRIVILEGES ON MCP.* TO 'archivematica'@'%' WITH GRANT OPTION; FLUSH PRIVILEGES;
```

### Secrets

Some other values need to be set as secrets in the OpenShift project:

```
oc create secret generic \
  --from-literal="MYSQL_ROOT_PASSWORD=12345" \
  archivematica-settings
```

## Docker images

You shouldn't need anything different here, as the provided `values.yaml` is set to use the docker images we build and push to our [container registry](https://gitlab.cern.ch/digitalmemory/archivematica-helm/container_registry).

- Archivematica images are unmodified versions from the upstream repositories, which are copied here as git submodules (check the `src` folders) and pinned to our target versions;
- The ClamAV image is a slightly modified version of the [base](https://github.com/Mailu/Mailu/tree/master/optional/clamav) one, we just added permissions on some common folders so the resulting container won't need to run under `root` (as we user will be the one set in `values.yaml` -> `general/user_uid`);
- More images are used (e.g. MySQL, Elastic, ..). For those, we use standard images published on canonical registries.

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

## References

- [PiqlConnect-AM](https://github.com/piql/PiqlConnect-AM)
