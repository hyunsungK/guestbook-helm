# guestbook-helm
This repo is for deploying guestbook application using helm.


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
✗ helm show values guestbook/charts/redis-17.3.8.tgz
```

### Application Configuration

- Configure values.yaml
```yaml
replicaCount: 1

image:
  repository: gcr.io/google-samples/gb-frontend
  pullPolicy: IfNotPresent
  tag: ""

imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""

serviceAccount:
  create: true
  annotations: {}
  name: ""

podAnnotations: {}
podSecurityContext: {}
securityContext: {}
service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  className: ""
  annotations: {}
  hosts:
    - host: chart-example.local
      paths: []
  tls: []

resources: {}
autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 100
  targetCPUUtilizationPercentage: 80

nodeSelector: {}
tolerations: []
affinity: {}

# 추가 영역
redis:
  fullnameOverride: redis
  usePassword: false
  configmap: |-
    appendonly no
```

- Installing application
```
✗ helm install my-guestbook guestbook -n chapter5
```

- Verifying release
```
✗ helm list -A
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
my-guestbook    chapter5        1               2022-11-08 17:06:48.366465 +0900 KST    deployed        guestbook-0.1.0 1.16.0
```

- Upgrading release
```
✗ helm upgrade my-guestbook ./guestbook -n chapter5
```

- Port forwarding pod
```
✗ export POD_NAME=$(kubectl get pods --namespace chapter5 -l "app.kubernetes.io/name=guestbook,app.kubernetes.io/instance=my-guestbook" -o jsonpath="{.items[0].metadata.name}")
✗ export CONTAINER_PORT=$(kubectl get pod --namespace chapter5 $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
✗ echo "Visit http://127.0.0.1:8080 to use your application"
✗ kubectl --namespace chapter5 port-forward $POD_NAME 8080:$CONTAINER_PORT
```

- Deleting namespace
```
✗ kubectl delete namespace chapter5
```

- Linting helm chart
```
✗ helm lint guestbook ./guestbook
```

- Dry running application
```
✗ helm install guestbook ./guestbook  --dry-run
```

## Think about it

P1. 왜 Jenkinsfile이 charts 상위에 위치하는가?
P2. charts 아래 `guestbook` 과 `nginx`가 별도로 있는 이유는?
```
.
└── charts
    ├── guestbook
    └── nginx
└── Jenkinsfile
```
