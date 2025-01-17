# CSI WekaFS Driver
Helm chart for Deployment of WekaIO Container Storage Interface (CSI) plugin for WekaFS - the world fastest filesystem

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Artifact HUB](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/csi-wekafs)](https://artifacthub.io/packages/search?repo=csi-wekafs)
![Version: 0.9.0-SNAPSHOT.87.4ecdb2b](https://img.shields.io/badge/Version-0.9.0--SNAPSHOT.87.4ecdb2b-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: v0.9.0-SNAPSHOT.87.4ecdb2b](https://img.shields.io/badge/AppVersion-v0.9.0--SNAPSHOT.87.4ecdb2b-informational?style=flat-square)

## Homepage
https://github.com/weka/csi-wekafs

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| WekaIO, Inc. | <csi@weka.io> | <https://weka.io> |

## Pre-requisite
- Kubernetes cluster of version 1.18 and up, 1.19 and up recommended
- Helm v3 must be installed and configured properly
- Weka system pre-configured and Weka client installed and registered in cluster for each Kubernetes node

## Deployment
```shell
helm repo add csi-wekafs https://weka.github.io/csi-wekafs
helm install csi-wekafsplugin csi-wekafs/csi-wekafsplugin --namespace csi-wekafsplugin --create-namespace [--set selinuxSupport=<off | mixed | enforced>]
```

> **NOTE:** Since version 0.8.0, Weka CSI plugin supports installation on SELinux-enabled Kubernetes clusters
> Refer to [SELinux Support & Installation Notes](https://github.com/weka/csi-wekafs/blob/master/selinux/README.md) for additional information

> **NOTE:** Since version 0.7.0, Weka CSI plugin transitions to API-based deployment model which requires API
> connectivity and credentials parameters to be set in Storage Class.
>
> Kubernetes does not allow storage class modification for existing volumes, hence the
> recommended upgrade process is re-deploying new persistent volumes based on new storage class format.
>
> However, for sake of more convenient migration, a `legacySecretName` parameter can be set that will
> bind existing legacy volumes to a Weka cluster API and allow volume expansion.
>
> For further information, refer [Official Weka CSI Plugin documentation](https://docs.weka.io/appendix/weka-csi-plugin)

## Usage
- [Deploy an Example application](https://github.com/weka/csi-wekafs/blob/master/docs/usage.md)
- [SELinux Support & Installation Notes](https://github.com/weka/csi-wekafs/blob/master/selinux/README.md)

## Additional Documentation
- [Official Weka CSI Plugin documentation](https://docs.weka.io/appendix/weka-csi-plugin)

## Requirements

Kubernetes: `>=1.18.0`

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| dynamicProvisionPath | string | `"csi-volumes"` | Directory in root of file system where dynamic volumes are provisioned |
| csiDriverName | string | `"csi.weka.io"` | Name of the driver (and provisioner) |
| csiDriverVersion | string | `"0.9.0-SNAPSHOT.87.4ecdb2b"` | CSI driver version |
| images.livenessprobesidecar | string | `"k8s.gcr.io/sig-storage/livenessprobe:v2.6.0"` | CSI liveness probe sidecar image URL |
| images.attachersidecar | string | `"k8s.gcr.io/sig-storage/csi-attacher:v3.4.0"` | CSI attacher sidecar image URL |
| images.provisionersidecar | string | `"k8s.gcr.io/sig-storage/csi-provisioner:v3.1.0"` | CSI provisioner sidecar image URL |
| images.registrarsidecar | string | `"k8s.gcr.io/sig-storage/csi-node-driver-registrar:v2.5.0"` | CSI registrar sidercar |
| images.resizersidecar | string | `"k8s.gcr.io/sig-storage/csi-resizer:v1.4.0"` | CSI resizer sidecar image URL |
| images.snapshottersidecar | string | `"k8s.gcr.io/sig-storage/csi-snapshotter:v6.2.1"` | CSI snapshotter sidecar image URL |
| images.csidriver | string | `"quay.io/weka.io/csi-wekafs"` | CSI driver main image URL |
| images.csidriverTag | string | `"0.9.0-SNAPSHOT.87.4ecdb2b"` | CSI driver tag |
| globalPluginTolerations | list | `[{"effect":"NoSchedule","key":"node-role.kubernetes.io/master","operator":"Exists"}]` | Tolerations for all CSI driver components |
| controllerPluginTolerations | list | `[{"effect":"NoSchedule","key":"node-role.kubernetes.io/master","operator":"Exists"}]` | Tolerations for CSI controller component only (by default same as global) |
| nodePluginTolerations | list | `[{"effect":"NoSchedule","key":"node-role.kubernetes.io/master","operator":"Exists"}]` | Tolerations for CSI node component only (by default same as global) |
| nodeSelector | object | `{}` | Optional nodeSelector for CSI plugin deployment on certain Kubernetes nodes only |
| logLevel | int | `5` | Log level of CSI plugin |
| useJsonLogging | bool | `false` | Use JSON structured logging instead of human-readable logging format (for exporting logs to structured log parser) |
| legacyVolumeSecretName | string | `""` | for migration of pre-CSI 0.7.0 volumes only, default API secret. Must reside in same namespace as the plugin |
| priorityClassName | string | `""` | Optional CSI Plugin priorityClassName |
| selinuxSupport | string | `"off"` | Support SELinux labeling for Persistent Volumes, may be either `off`, `mixed`, `enforced` (default off)    In `enforced` mode, CSI node components will only start on nodes having a label `selinuxNodeLabel` below    In `mixed` mode, separate CSI node components will be installed on SELinux-enabled and regular hosts    In `off` mode, only non-SELinux-enabled node components will be run on hosts without label.    WARNING: if SELinux is not enabled, volume provisioning and publishing might fail! |
| selinuxNodeLabel | string | `"csi.weka.io/selinux_enabled"` | This label must be set to `"true"` on SELinux-enabled Kubernetes nodes,    e.g., to run the node server in secure mode on SELinux-enabled node, the node must have label    `csi.weka.io/selinux_enabled="true"` |
| kubeletPath | string | `"/var/lib/kubelet"` | kubelet path, in cases Kubernetes is installed not in default folder |
| metrics.enabled | bool | `true` | Enable Prometheus Metrics |
| metrics.port | int | `9090` | Metrics port |
| pluginConfig.allowInsecureHttps | bool | `false` | Allow insecure HTTPS (skip TLS certificate verification) |
| pluginConfig.objectNaming.volumePrefix | string | `"csivol-"` | Prefix that will be added to names of Weka cluster filesystems / snapshots assocciated with CSI volume, must not exceed 7 symbols. |
| pluginConfig.objectNaming.snapshotPrefix | string | `"csisnp-"` | Prefix that will be added to names of Weka cluster snapshots assocciated with CSI snapshot, must not exceed 7 symbols. |
| pluginConfig.objectNaming.seedSnapshotPrefix | string | `"csisnp-seed-"` | Prefix that will be added to automatically created "seed" snapshot of empty filesytem, must not exceed 12 symbols. |
| pluginConfig.allowedOperations.autoCreateFilesystems | bool | `true` | Allow automatic provisioning of CSI volumes based on distinct Weka filesystem |
| pluginConfig.allowedOperations.autoExpandFilesystems | bool | `true` | Allow automatic expansion of filesystem on which Weka snapshot-based CSI volumes, e.g. in case a required volume capacity exceeds the size of filesystem. Note: the filesystem is not expanded automatically when a new directory-based volume is provisioned |
| pluginConfig.allowedOperations.autoCreateSeedSnapshot | bool | `true` | Allow automatic creation of "empty" snapshots for any provisioned filesystem, such snapshots could allow creation of additional empty CSI volumes to originate from the snapshot |
| pluginConfig.allowedOperations.snapshotDirectoryVolumes | bool | `false` | Create snapshots of legacy (dir/v1) volumes. By default disabled. Note: when enabled, for every legacy volume snapshot, a full filesystem snapshot will be created (wasteful) |
| pluginConfig.allowedOperations.snapshotVolumesWithoutQuotaEnforcement | bool | `false` | Allow creation of snapshot-based volumes even on unsupported Weka cluster versions, off by default Note: On versions of Weka < v4.2 snapshot-based volume capacity cannot be enforced |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.11.0](https://github.com/norwoodj/helm-docs/releases/v1.11.0)
