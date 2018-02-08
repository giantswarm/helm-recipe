
# helm recipe

You have a set of fine crafted manifests for your cluster. These can be applied with one command:
```bash
kubectl apply --recursive --filename ./manifests
```

Placing them in a git repository add a history of your changes and allows shared discussion of future changes via pull requests.

When the need comes up to run multiple clusters with a similar software stack for staging and testing the manifests might need some small changes maybe at many places. For example container image version, ingress url, authentication to services, volumes to point at test data or even conigmaps to test different settings.

Adopting manifests manually for different clusters quickly becomes cumbersome.

Helm provides you templating for manifests and much more.

Instead of running the whole Helm setup we will focus here just on the templating feature and run this locally.

One possible directory structure:

```
./helm
  ./charts
    ./prometheus-operator
    ./prometheus
  ./values
    ./prometheus-operator
    ./prometheus  
./manifests
  ./prometheus-operator
  ./prometheus
```

Prepare some values:
```bash
set chart_name "etcd-operator"
set chart_repo "https://kubernetes-charts.storage.googleapis.com"
set chart_version "0.6.2"
set release_name "r1"
```

At first we will fetch the upstream template to `./helm/charts/prometheus-operator`.
Look around <link>
Usually comes with readme, explanation values
```bash
helm fetch \
  --repo "$chart_repo" \
  --untar \
  --untardir ./helm/charts \
  --version "$chart_version" \
    "$chart_name"
```

Then we want to copy and maybe adjust the file used to provide the replacement values:
to `./helm/values/prometheus-operator`.
```bash
cp \
  "./helm/charts/$chart_name/values.yaml" \
  "./helm/values/$chart_name/values.yaml"
```

Combining the template with our adjusted values.
Render our final manifests to be applied to the cluster:
```bash
helm template \
  --name "$release_name" \
  --values "./helm/values/$chart_name/values.yaml" \
  --output-dir ./manifests \
    "./helm/charts/$chart_name"

```

Inspect again


Maybe values not enough, adjust template
-> send upstream :)


# Chart Repositories

- official
  + pull-requests
- CoreOS?
  prometheus
- https://github.com/banzaicloud/banzai-charts/tree/master/stable/vault ?

# Tiller

Additional features like:
- no need to fetch
- roll forward/backward?
- hooks
- run tests


--- alternative workflow
fetch new template version
for each directory with values.yaml get dir-name
  render to manifests dir
end


--- see also
https://github.com/kubernetes/helm/issues/3089
https://github.com/bitnami-labs/helm-crd
https://github.com/burdiyan/helm-update-config/
https://github.com/eneco/landscaper
https://github.com/roboll/helmfile
