# LocalEGA-helm

## Table of Contents

- [Prerequisites](#Prerequisites)
  - [Optional, add S3 storage](#Optional-add-S3-storage)
- [Installing the Chart](#Installing-the-Chart)
  - [Configuration](#Configuration)
- [Uninstalling the Chart](#Uninstalling-the-Chart)

Helm chart to deploy LocalEGA to any kubernetes cluster.

## Prerequisites

Before deploying the Helm charts HELM should be initialized and the tiller installed, following [these](https://docs.helm.sh/using_helm#initialize-helm-and-install-tiller) instructions.

When deploying for the first time you need to create the secrets using the `deploy.py` script from [LocalEGA-deploy-k8s](https://github.com/NBISweden/LocalEGA-deploy-k8s).

```console
python3 {/PATH/TO/LocalEGA-deploy-k8s}/auto/deploy.py --config-path localega/
```

### Optional, add S3 storage

LocalEGA relies on a S3 type backend storage as data archive.  
If the kubernetes environment doesn't supply a S3 storage it can be added using either [minio](https://www.minio.io/) or [ceph](https://www.ceph.org).

#### Minio

See the [documentation](https://github.com/helm/charts/tree/master/stable/minio) for how to configure minio.  
More specifically the size of each backing volume and if minio should run in distributed mode or not.

```console
helm install --namespace localega --set accessKey=s3_access,secretKey=s3_secret stable/minio
```

## Installing the Chart

You can install the Chart via Helm CLI:

```console
helm install localega --namespace localega --values localega/config/trace.yml
```

### Configuration

The following table lists the configurable parameters of the `localega` chart and their default values.

Parameter | Description | Default
--------- | ----------- | -------
`base.repository` | LocalEGA container image repository | `nbisweden/ega-base`
`base.imageTag` | LocalEGA container image tag | `latest`
`base.imagePullPolicy` | LocalEGA container image pull policy | `Always`
`config.deploy` | If true, deploy ConfigMaps | `true`
`config.broker_connection_attempts` | Connection attempts before timing out | `30`
`config.broker_retry_delay` | time in seconds between each connection attempt | `10`
`config.broker_username` | username for accessing local MQ service | `""`
`config.cega_endpoint` | RUL to CEGA user service | `""`
`config.cega_endpoint_json` | Prefix in the JSON response that contains the user info| `response.result`
`config.cega_mq_host` | CEGA MQ hostname | `""`
`config.cega_vhost` | CEGA MQ vhost path | `""`
`config.cega_port` | CEGA MQ acces port | `5271`
`config.cega_username` | CEGA MQ username | `""`
`config.postgres_db_name` | Database name | `lega`
`config.postgres_host` | Database hostname or IP address | `""`
`config.postgres_try` | Database connection attempts | `30`
`config.postgres_user` | Database username | `""`
`config.vault_driver` | Backend storage type, `S3Storage` or `file` | `S3Storage`
`config.vault_url` | URL to backed storage | `""`
`persistence.enabled`| If true, create a Persistent Volume Claim for all services that require it| `true`
`persistence.storageClass` | Storage Class for all Persistent volume Claims, use "local-storage" for local backed storage | `""`
`secrets.deploy` | If true, deploy secrets | `true`
`secrets.keys_password` | Shared LocalEGA PGP password | `""`
`secrets.lega_password` | LocalEGA password | `""`
`secrets.postgres_password` | Password to LocalEGA sql database | `""`
`secrets.s3_access` | Access key to S3 storage | `""`
`secrets.s3_secret` | Secret key to S3 storage  | `""`
`inbox.name` | inbox container name | `inbox`
`inbox.repository` | inbox container image repository | `nbisweden/ega-mina-inbox`
`inbox.imageTag` | inbox container image version | `latest`
`inbox.imagePullPolicy` | inbox container image pull policy | `IfNotPresent`
`inbox.inboxPath` | Path on mounted volume for inbox data | `/`
`inbox.replicaCount` | desired number of inboxes | `1`
`inbox.persistence.existingClaim` | inbox data Persistent Volume existing claim name | `""`
`inbox.persistence.storageSize` | inbox persistent volume size | `1Gi`
`ingest.name` | ingest container name | `ingest`
`ingest.replicaCount` | desired number of ingest workers | `1`
`keys.deploy` | Set to false if using a external keyserver | `true`
`keys.repository` | Keyserver container image repository | `cscfi/ega-keyserver`
`keys.imageTag` | Keyserver container image version | `latest`
`keys.imagePullPolicy` | Keyserver container image pull policy | `IfNotPresent`
`keys.port` | Keyserver port | `8443`
`mq.name` | rabbitmq container name | `rabbitmq`
`mq.repository` | rabbitmq container image repository | `rabbitmq`
`mq.imageTag` | rabbitmq container image pull policy | `3.6-management-alpine`
`mq.imagePullPolicy` | rabbitmq container image pull policy | `IfNotPresent`
`mq.persistence.existingClaim` | rabbitmq data Persistent Volume existing claim name | `""`
`mq.persistence.storageSize` | rabbitmq persistent volume size | `1Gi`
`mq.replicaCount` | desired number of rabbitmqes | `1`
`mq.port` | rabbitmq port | `5672`
`mq.metrics.enabled` | enable metrics for rabbirmq service | `true`
`mq.metrics.repository` | rabbitmq metrics exporter repository | `kbudde/rabbitmq-exporter`
`mq.metrics.imageTag` | rabbitmq metrics exporter version | `v0.29.0`
`mq.metrics.imagePullPolicy` | rabbitmq metrics exporter image pull policy | `IfNotPresent`
`postgres.deploy` | Set to false if using a external database | `true`
`postgres.name` | postgreSQL container name | `postgres`
`postgres.repository` | postgreSQL container image repository | `postgres`
`postgres.imageTag` | postgreSQL container image version | `10.5-apline`
`postgres.imagePullPolicy` | postgreSQL container image pull policy | `IfNotPresent`
`postgres.persistence.existingClaim` | postgres data Persistent Volume existing claim name | `""`
`postgres.persistence.storageSize` | postgres persistent volume size | `1Gi`
`postgres.replicaCount` | desired number of postgreses | `1`
`postgres.metrics.enabled` | enable metrics for postgreSQL service | `true`
`postgres.metrics.repository` | postgreSQL metrics exporter repository | `wrouesnel/postgres_exporter`
`postgres.metrics.imageTag` | postgreSQL metrics exporter version | `v0.4.6`
`postgres.metrics.imagePullPolicy` | postgreSQL metrics exporter image pull policy | `IfNotPresent`
`res.repository`| RES container image repository | `cscfi/ega-res`
`res.imageTag`| RES container image version | `latest`
`res.imagePullPolicy`| RES container image pull policy | `IfNotPresent`
`res.replicaCount`| desired number of RES containers | `1`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
helm install localega --set postgres.imageTag=10 --values localega/config/trace.yml

```

## Install fake CEGA

When testing we can deploy a fake CentralEGA to takl to.

```console
helm install cega --namespace localega --values cega/config/trace.yml
```

## Uninstalling the Chart

You can uninstall the Chart via Helm CLI:

```console
helm delete ${deployment name} --purge
```