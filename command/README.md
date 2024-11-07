## Helm
Output helm chart to yaml
```
helm template fluent-bit fluent/fluent-bit -f /the/path/to/values.yaml -n ${namespace} > test.yaml
```
