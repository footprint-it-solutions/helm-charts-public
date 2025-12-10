# Aerith Helm Chart

This Helm chart deploys the Aerith Kubernetes operator, which automates the process of ingesting engineering tasks (e.g., from Trello) and generating solutions using Google Gemini.

## Prerequisites

*   Kubernetes 1.25+
*   Helm 3+
*   Trello API credentials (API Key, Token, Board ID)
*   Google Gemini API Key

## Installing the Chart

To install the chart with the release name `aerith-operator`:

```bash
helm upgrade --install aerith-operator helm-charts-public/charts/aerith \
  --namespace aerith-system --create-namespace \
  --set manager.env[0].valueFrom.secretKeyRef.name=trello-credentials \
  --set manager.env[0].valueFrom.secretKeyRef.key=TRELLO_API_KEY \
  --set manager.env[1].valueFrom.secretKeyRef.name=trello-credentials \
  --set manager.env[1].valueFrom.secretKeyRef.key=TRELLO_TOKEN \
  --set manager.env[2].valueFrom.secretKeyRef.name=trello-credentials \
  --set manager.env[2].valueFrom.secretKeyRef.key=TRELLO_BOARD_ID \
  --set manager.env[3].valueFrom.secretKeyRef.name=gemini-credentials \
  --set manager.env[3].valueFrom.secretKeyRef.key=GEMINI_API_KEY \
  --set manager.env[4].value="gemini-2.0-flash-exp" # Optional, default is set in values.yaml
```

### Configuration

The following table lists the configurable parameters of the Aerith chart and their default values.

| Key | Type | Default | Description |
|---|---|---|---|
| `replicaCount` | int | `1` | Number of controller replicas |
| `image.repository` | string | `footprint-it-solutions/aerith-controller` | Controller image repository |
| `image.pullPolicy` | string | `IfNotPresent` | Controller image pull policy |
| `image.tag` | string | `v0.0.1` | Controller image tag. Defaults to `appVersion`. |
| `manager.args` | list | `["--leader-elect"]` | Arguments passed to the controller manager |
| `manager.env` | list | `[]` | Environment variables for the controller manager. Use `valueFrom.secretKeyRef` for secrets. |
| `manager.healthProbePort` | int | `8081` | Port for the health probe |
| `manager.metricsPort` | int | `8080` | Port for controller metrics |
| `manager.secureMetrics` | bool | `false` | Enable secure metrics |
| `manager.containerPort` | int | `9443` | Webhook server port |
| `serviceAccount.create` | bool | `true` | Specifies whether a service account should be created |
| `serviceAccount.annotations` | object | `{}` | Annotations to add to the service account |
| `serviceAccount.name` | string | `""` | The name of the service account to use. If not set and create is true, a name is generated. |
| `rbac.create` | bool | `true` | Create RBAC resources (ClusterRole, ClusterRoleBinding) |
| `crds.install` | bool | `true` | Install Custom Resource Definitions (CRDs) with the chart. Set to `false` if CRDs are managed separately. |
| `solutionGenerator.enabled` | bool | `false` | Enable deployment of the solution-generator as a Kubernetes Job. |
| `solutionGenerator.image.repository` | string | `footprint-it-solutions/aerith-solution-generator` | Solution generator image repository |
| `solutionGenerator.image.pullPolicy` | string | `IfNotPresent` | Solution generator image pull policy |
| `solutionGenerator.image.tag` | string | `v0.0.1` | Solution generator image tag. Defaults to `appVersion`. |
| `resources` | object | `{}` | Container resource limits and requests |
| `nodeSelector` | object | `{}` | Node selector for pod assignment |
| `tolerations` | list | `[]` | Tolerations for pod scheduling |
| `affinity` | object | `{}` | Affinity rules for pod scheduling |

Specify parameters using `--set key=value[,key=value]` on the `helm upgrade --install` command. For example:

```bash
helm upgrade --install aerith-operator ./aerith \
  --namespace aerith-system \
  --set replicaCount=2 \
  --set image.tag="v0.0.2"
```

Alternatively, a YAML file that specifies the values for the parameters can be provided:

```bash
helm upgrade --install aerith-operator ./aerith -f values.yaml
