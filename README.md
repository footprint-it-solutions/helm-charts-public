# helm-charts-public

## Usage

### Adding charts

* Add Helm chart to the [charts](./charts) directory
* Package the chart, e.g. `helm package charts/netbird-peer`
* Update the repo index, i.e. `helm repo index .`
* Commit and push the changes

### Using the repo

`helm repo add footprint-it-solutions https://footprint-it-solutions.github.io/helm-charts-public/`

You can then browse the charts and versions available, and install them on target Kubernetes clusters
as normal. See [documentation](https://helm.sh/docs/helm/helm_install/).
