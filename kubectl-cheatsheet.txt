# kubectl cheat sheet

### 全Node IP取得
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}'

### 最初のNode IP取得
kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="ExternalIP")].address}'

### Patch(Container Image)
kubectl patch -n velero deployment velero -p '{"spec":{"template":{"spec":{"containers":[{"name":"velero","image":"HARBOR-FQDN/library/velero:v1.2.0"}]}}}}'  

### Patch(InitContainer Image)
kubectl patch -n velero deployment velero -p '{"spec":{"template":{"spec":{"initContainers":[{"name":"velero-plugin-for-aws","image":"HARBOR-FQDN/library/velero-plugin-for-aws:v1.0.0"}]}}}}'  
