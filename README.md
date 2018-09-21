# LocalEGA-helm

## Table of Contents

- [Prerequisites](#Prerequisites)
  - [Optional, add S3 storage](#Optional-add-S3-storage)
- [Installing the Chart](#Installing-the-Chart)
  - [Configuration](#Configuration)
- [Uninstalling the Chart](#Uninstalling-the-Chart)

Helm chart to deploy LocalEGA to any kubernetes cluster.

## Prerequisites

Before deploying the Helm charts HELM should be initialized and the tiller installed, following [these ](https://docs.helm.sh/using_helm#initialize-helm-and-install-tiller)instructions.

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
`deploy.cega` | If true deploy fake CEGA | `false`
`deploy.config` | If true, deploy ConfigMaps | `true`
`deploy.secrets` | If true, deploy secrets | `true`
`persistence.enabled`| If true, create a Persistent Volume Claim for all services that require it| `true`
`persistence.storageClass` | Storage Class for all Persistent volume Claims, use "local-storage" for local backed storage | `""`
`secrets.cega_address` | URL to CentralEGA server | `""`
`secrets.cega_creds` | Credentials to CentralEGA server | `""`
`secrets.cega_mq_pass` | Password to CentralEGAs rabbitmq | `""`
`secrets.keys_password` | Keyserver  | `""`
`secrets.lega_password` | LocalEGA password | `""`
`secrets.postgres_password` | Password to LocalEGA DB | `""`
`secrets.s3_access` | Access key to S3 storage | `""`
`secrets.s3_secret` | Secret key to S3 storage  | `""`
`inbox.name` | inbox container name | `inbox`
`inbox.repository` | inbox container image repository | `nbisweden/ega-mina-inbox`
`inbox.imageTag` | inbox container image pull policy | `latest`
`inbox.imagePullPolicy` | inbox container image pull policy | `IfNotPresent`
`inbox.inboxPath` | Path on mounted volume for inbox data | `/`
`inbox.replicaCount` | desired number of inboxes | `1`
`inbox.persistence.existingClaim` | inbox data Persistent Volume existing claim name | ``
`inbox.persistence.storageSize` | inbox persistent volume size | `1Gi`
`ingest.name` | ingest container name | `ingest`
`ingest.replicaCount` | desired number of ingest workers | `1`
`postgres.name` | postgreSQL container name | `postgres`
`postgres.repository` | postgreSQL container image repository | `postgres`
`postgres.imageTag` | postgreSQL container image pull policy | `9.6-apline`
`postgres.imagePullPolicy` | postgreSQL container image pull policy | `IfNotPresent`
`postgres.replicaCount` | desired number of postgreses | `1`
`postgres.persistence.existingClaim` | postgres data Persistent Volume existing claim name | ``
`postgres.persistence.storageSize` | postgres persistent volume size | `1Gi`
`rabbitmq.name` | rabbitmq container name | `rabbitmq`
`rabbitmq.repository` | rabbitmq container image repository | `rabbitmq`
`rabbitmq.imageTag` | rabbitmq container image pull policy | `3.6-management-alpine`
`rabbitmq.imagePullPolicy` | rabbitmq container image pull policy | `IfNotPresent`
`rabbitmq.replicaCount` | desired number of rabbitmqes | `1`
`rabbitmq.persistence.existingClaim` | rabbitmq data Persistent Volume existing claim name | ``
`rabbitmq.persistence.storageSize` | rabbitmq persistent volume size | `1Gi`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
helm install localega --set deploy.cega=true --values localega/config/trace.yml
```

## Uninstalling the Chart

You can uninstall the Chart via Helm CLI:

```console
helm delete ${deployment name} --purge
```