# pods not creating
when following the guide and the splunk Operator pod is created but the pod is not running you will see in the logs that there is no pv connected to the pod therefore it is not creating. this only happens if there is no created pv yet for splunk to connect to 

```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
```
