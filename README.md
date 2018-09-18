# LocalEGA-helm

## Table of Contents

- [Prerequisites](#Prerequisites)
- [Installing the Chart](#Installing-the-Chart)
  - [Configuration](#Configuration)
- [Uninstalling the Chart](#Uninstalling-the-Chart)

Helm chart to deploy LocalEGA to any kubernetes cluster.

## Prerequisites

When deploying for the first time you need to create the secrets using the `deploy.pl` script from LocalEGA-deploy-k8s.

```console
python3 ../auto/deploy.pl --config-path localega/
```

## Installing the Chart

You can install the Chart via Helm CLI:

```console
helm install localega --values localega/config/trace.yaml
```

### Configuration

The following table lists the configurable parameters of the localega chart and their default values.

Parameter | Description | Default
--------- | ----------- | -------
`base.repository` | rabbitmq container image repository | `nbisweden/ega-base`
`base.imageTag` | rabbitmq container image pull policy | `latest`
`base.imagePullPolicy` | rabbitmq container image pull policy | `Always`
`deploy.cega` | If true deploy fake CEGA | `false`
`deploy.config` | If true, deploy ConfigMaps | `true`
`deploy.secrets` | If true, deploy secrets | `true`
`persistence.enabled`| If true, create a Persistent Volume Claim for all services that require it| `true`
`persistence.storageClass` | Storage Class for all Persistent volume Claims, use "local-storage" for local backed storage | `""`
`secrets.cega_address` | Url to CentralEGA server | `""`
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
`postgres.name` | postgres container name | `postgres`
`postgres.repository` | postgres container image repository | `postgres`
`postgres.imageTag` | postgres container image pull policy | `10-apline`
`postgres.imagePullPolicy` | postgres container image pull policy | `IfNotPresent`
`postgres.replicaCount` | desired number of postgreses | `1`
`postgres.persistence.existingClaim` | postgres data Persistent Volume existing claim name | ``
`postgres.persistence.storageSize` | postgres persistent volume size | `1Gi`
`rabbitmq.name` | rabbitmq container name | `rabbitmq`
`rabbitmq.repository` | rabbitmq container image repository | `rabbitmq`
`rabbitmq.imageTag` | rabbitmq container image pull policy | `3.6.14-management`
`rabbitmq.imagePullPolicy` | rabbitmq container image pull policy | `IfNotPresent`
`rabbitmq.replicaCount` | desired number of rabbitmqes | `1`
`rabbitmq.persistence.existingClaim` | rabbitmq data Persistent Volume existing claim name | ``
`rabbitmq.persistence.storageSize` | rabbitmq persistent volume size | `1Gi`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
helm install localega --set deploy.cega=true --values localega/config/trace.yaml
```

## Uninstalling the Chart

You can uninstall the Chart via Helm CLI:

```console
helm delete ${deployment name} --purge
```