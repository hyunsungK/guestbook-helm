# guestbook-helm

## Goal
- Understanding the Guestbook application
- Creating a Guestbook Helm chart
- Improving the Guestbook Helm chart
- Publishing the Guestbook chart to a chart repository



## Helm Chart
helm chart의 예제로 guestbook을 만들어 보는 과정을 진행 예정이다.
- [guestbook](https://github.com/PacktPublishing/-Learn-Helm/tree/master/helm-charts/charts/guestbook)


## Getting Started


### Creating namesapce
```
✗ kubectl create namespace chapter5
namespace/chapter5 created
```

### Creating Helm chart
- creating helm chart
```
✗ helm create guestbook
guestbook
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml
```

- delete tests
```
✗ rm -rf guestbook/templates/tests
```

### Adding a Redis chart
- adding bitnami repo
```
✗ helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
```

- search redis chart
```
✗ helm search repo redis --versions
NAME                    CHART VERSION   APP VERSION
bitnami/redis           17.3.8          7.0.5
...
bitnami/redis-cluster   7.1.1           6.2.6
```

### Adding redis dependency

- Modify `Chart.yaml` file
```Chart.yaml
...
dependencies:
  - name: redis
    version: 17.3.8
    repository: https://charts.bitnami.com/bitnami
```

- Update guestbook
```
helm dependency update guestbook
✗ tree guestbook/charts
guestbook/charts
└── redis-17.3.8.tgz
```

- show value
```
helm show values guestbook/charts/redis-17.3.8.tgz
```
