# Rocket.Chat
[![Artifact HUB](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/dteam-helm-charts)](https://artifacthub.io/packages/search?repo=dteam-helm-charts)

[Rocket.Chat](https://rocket.chat/) is free, unlimited and open source. Replace email, HipChat & Slack with the ultimate team chat software solution.

## Installation

```bash
helm repo add dteam https://dteamdev.github.io/charts
helm install dteam/rocketchat -n rocketchat -f your.values.yaml
```

## Credits

This chart based on [rocketchat](https://github.com/verwilst/helm-charts) helm chart by [@verwilst](https://github.com/verwilst)

## Introduction

This chart bootstraps a [Rocket.Chat](https://rocket.chat/) Deployment on a [Kubernetes](https://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager. It provisions a fully featured Rocket.Chat installation.

In addition, this chart supports scaling of Rocket.Chat for increased server capacity and high availability.  For more information on Rocket.Chat and its capabilities, see its [documentation](https://rocket.chat/docs/).

## Prerequisites Details

The chart has an optional dependency on the [MongoDB](https://github.com/kubernetes/charts/tree/master/stable/mongodb) chart.
By default, the MongoDB chart requires PV support on underlying infrastructure (may be disabled).



## Configuration

The following table lists the configurable parameters of the Rocket.Chat chart and their default values.

Parameter | Description | Default
--- | --- | ---
`image.repository` | Image repository | `docker.io/rocketchat/rocket.chat`
`image.tag` | Image tag | `3.10.3`
`image.pullPolicy` | Image pull policy | `IfNotPresent`
`host` | Hostname for Rocket.Chat. Also used for ingress (if enabled) | `""`
`replicaCount` | Number of replicas to run | `1`
`smtp.enabled` | Enable SMTP for sending mails | `false`
`smtp.username` | Username of the SMTP account | `""`
`smtp.password` | Password of the SMTP account | `""`
`smtp.host` | Hostname of the SMTP server | `""`
`smtp.port` | Port of the SMTP server | `587`
`extraEnv` | Extra environment variables for Rocket.Chat. Used with `tpl` function, so this needs to be a string | `""`
`podAntiAffinity` | Pod anti-affinity can prevent the scheduler from placing RocketChat replicas on the same node. The default value "soft" means that the scheduler should *prefer* to not schedule two replica pods onto the same node but no guarantee is provided. The value "hard" means that the scheduler is *required* to not schedule two replica pods onto the same node. The value "" will disable pod anti-affinity so that no anti-affinity rules will be configured. | `""` |
`podAntiAffinityTopologyKey` | If anti-affinity is enabled sets the topologyKey to use for anti-affinity. This can be changed to, for example `failure-domain.beta.kubernetes.io/zone`| `kubernetes.io/hostname` |
| `affinity` | Assign custom affinity rules to the RocketChat instance https://kubernetes.io/docs/concepts/configuration/assign-pod-node/ | `{}` |
`minAvailable` | Minimum number / percentage of pods that should remain scheduled | `1` |
`externalMongodbUrl` | MongoDB URL if using an externally provisioned MongoDB | `""`
`externalMongodbOplogUrl` | MongoDB OpLog URL if using an externally provisioned MongoDB. Required if `externalMongodbUrl` is set | `""`
`mongodb.enabled` | Enable or disable MongoDB dependency. Refer to the [stable/mongodb docs](https://github.com/helm/charts/tree/master/stable/mongodb#configuration) for more information | `true`
`persistence.enabled` | Enable persistence using a PVC. This is not necessary if you're using the default (and recommended) [GridFS](https://rocket.chat/docs/administrator-guides/file-upload/) file storage | `true`
`persistence.storageClass` | Storage class of the PVC to use | `""`
`persistence.accessMode` | Access mode of the PVC | `ReadWriteOnce`
`persistence.size` | Size of the PVC | `8Gi`
`persistence.existingClaim` | An Existing PVC name for rocketchat volume | `""`
`resources` | Pod resource requests and limits | `{}`
`securityContext.enabled` | Enable security context for the pod | `true`
`securityContext.runAsUser` | User to run the pod as | `999`
`securityContext.fsGroup` | fs group to use for the pod | `999`
`serviceAccount.create` | Specifies whether a ServiceAccount should be created | `true`
`serviceAccount.name` | Name of the ServiceAccount to use. If not set and create is true, a name is generated using the fullname template | `""`
`ingress.enabled` | If `true`, an ingress is created | `false`
`ingress.annotations` | Annotations for the ingress | `{}`
`ingress.path` | Path of the ingress | `/`
`ingress.tls` | A list of [IngressTLS](https://kubernetes.io/docs/reference/federation/extensions/v1beta1/definitions/#_v1beta1_ingresstls) items | `[]`
`livenessProbe.enabled` | Turn on and off liveness probe | `true`
`livenessProbe.initialDelaySeconds` | Delay before liveness probe is initiated | `60`
`livenessProbe.periodSeconds` | How often to perform the probe | `15`
`livenessProbe.timeoutSeconds` | When the probe times out | `5`
`livenessProbe.failureThreshold` | Minimum consecutive failures for the probe | `3`
`livenessProbe.successThreshold` | Minimum consecutive successes for the probe | `1`
`readinessProbe.enabled` | Turn on and off readiness probe | `true`
`readinessProbe.initialDelaySeconds` | Delay before readiness probe is initiated | `10`
`readinessProbe.periodSeconds` | How often to perform the probe | `15`
`readinessProbe.timeoutSeconds` | When the probe times out | `5`
`readinessProbe.failureThreshold` | Minimum consecutive failures for the probe | `3`
`readinessProbe.successThreshold` | Minimum consecutive successes for the probe | `1`
`service.annotations` | Annotations for the Rocket.Chat service | `{}`
`service.labels` | Additional labels for the Rocket.Chat service | `{}`
`service.type` | The service type to use | `ClusterIP`
`service.port` | The service port | `80`
`service.nodePort` | The node port used if the service is of type `NodePort` | `""`
`podLabels` | Additional pod labels for the Rocket.Chat pods | `{}`
`podAnnotations` | Additional pod annotations for the Rocket.Chat pods | `{}`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name rocketchat -f values.yaml dteam/rocketchat
```

### Database Setup

Rocket.Chat uses a MongoDB instance to presist its data.
By default, the [MongoDB](https://github.com/kubernetes/charts/tree/master/stable/mongodb) chart is deployed and a single MongoDB instance is created as the primary in a replicaset.  
Please refer to this chart for additional MongoDB configuration options.
If you are using chart defaults, make sure to set at least the `mongodb.mongodbRootPassword`, `mongodb.mongodbUsername` and `mongodb.mongodbPassword` values. 
> **WARNING**: The root credentials are used to connect to the MongoDB OpLog

#### Using an External Database

This chart supports using an existing MongoDB instance. Use the `` configuration options and disable MongoDB with `--set mongodb.enabled=false`

### Configuring Additional Environment Variables

```yaml
extraEnv: |
  - name: MONGO_OPTIONS
    value: '{"ssl": "true"}'
```

### Increasing Server Capacity and HA Setup

To increase the capacity of the server, you can scale up the number of Rocket.Chat server instances across available computing resources in your cluster, for example,

```bash
$ kubectl scale --replicas=3 deployment/rocketchat
```

By default, this chart creates one MongoDB instance as a Primary in a replicaset.  This is the minimum requirement to run Rocket.Chat 1.x+.    You can also scale up the capacity and availability of the MongoDB cluster independently.  Please see the [MongoDB chart](https://github.com/kubernetes/charts/tree/master/stable/mongodb) for configuration information.

For information on running Rocket.Chat in scaled configurations, see the [documentation](https://rocket.chat/docs/installation/docker-containers/high-availability-install/#guide-to-install-rocketchat-as-ha-with-mongodb-replicaset-as-backend) for more details.

```console
$ kubectl delete deployment rocketchat-rocketchat --cascade=false
```

## Introduction

This chart deploys a multiple instances of keycloak gatekeeper authentication server

## Configuration options

There is defined only basic options. For all options look at `values.yaml` file

| Parameter                         | Description                                                                       | Default       |
| ----------------------------------| ----------------------------------------------------------------------------------| :-------------------------------: |
| `ingress.enabled`                 | Enable ingress                                                                    | `false`                           |
| `ingress.annotations`             | Specify the ingress annotations                                                   | `{}`                              |
| `discoveryUrl`                    | Discovery URL of your OIDC server                                                 | `""`                              |
| `clientId`                        | Client ID for your OIDC server (for all proxies)                                  | `http://example.com:6443`         |
| `clientSecret`                    | Client secret for your OIDC server (for all proxies)                              | `http://example.com/auth`         |
| `encryptionKey`                   | cookie encryption key (for all proxies, sets randomly if unspecified)             | `http://example.com/token`        |
| `enableRefreshTokens`             | Enable refresh tokens                                                             | `http://example.com/userinfo`     |
| `proxies`                         | Proxies array                                                                     | `[]"`                             |
| `proxies[0].name`                 | Proxy name                                                                        | `""`                              |
| `proxies[0].namespace`            | Proxy namespace                                                                   | `""`                              |
| `proxies[0].hosts`                | List of proxies hosts for ingress                                                 | `[]`                              |
| `proxies[0].tls`                  | Proxies tls config for ingress                                                    | `[]`                              |
| `proxies[0].tls[0].secretName`    | Tls secret name                                                                   | `""`                              |
| `proxies[0].tls[0].hosts`         | Tls hosts                                                                         | `""`                              |
| `proxies[0].target`               | Target application url (Example: https://kubernetes-dahsboard)                    | `""`                              |
| `proxies[0].resources`            | Resources spec. Look in `values.yaml` for a link to examples                      | `""`                              |
| `proxies[0].clientId`             | Client ID for your OIDC server (for this proxy, sets randomly if unspecified)     | `""`                              |
| `proxies[0].clientSecret`         | Client secret for your OIDC server (for this proxy, set randomly if unspecified)  | `""`                              |
| `proxies[0].encryptionKey`        | cookie encryption key (for this proxy, sets randomly if unspecified)              | `""`                              |