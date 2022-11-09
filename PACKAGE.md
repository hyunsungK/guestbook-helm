# Package Guestbook
패키지 사용하는 방법

## How to package?

- Packaging Helm files
```bash
helm package ./guestbook
```

- Indexing yaml
```bash
helm repo index .
```

## Creating repository and associate repo
- Registering a repository
```bash
helm repo add myrepo <GITHUB URL>
```


## Searching and Installing application

- Searching helm
```
helm search repo guestbook
```

- Installing guestbook application
```
helm install guestbook myrepo/guestbook
```

## Reference
- [Best Practice](https://yunsangjun.github.io/helm/2018/05/26/hosting-helm-chart-repository.html)
