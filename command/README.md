## Helm
Update
```
helm repo update
```

Search repo versions
```
helm search repo signoz --versions
```

Output helm chart to yaml
```
helm template fluent-bit fluent/fluent-bit -f /the/path/to/values.yaml -n ${namespace} > test.yaml
```
