# error: helm install prometheus prometheus-community/kube-prometheus-stack

use this installation code as the first link installs everything and it does not overwrite the values.yaml the second is for prometheus exclusively and it follows the configuration on the values.yaml
```bash
helm install prometheus prometheus-community/prometheus -f  <values.yaml> --namespace=prometheus-operator --create-namespace
```
