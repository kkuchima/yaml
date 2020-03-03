# Velero Demo(Quick Start for private Docker Registry)

### MinIOデプロイ ※NodePortに変更
kubectl apply -f examples/minio/00-minio-deployment.yaml

### Node IP/NodePort取得
NODEIP=`kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="ExternalIP")].address}'`  
NODEPORT=`kubectl get svc minio -n velero -o jsonpath='{.spec.ports[0].nodePort}'`  

### Velero Install
velero install \
    --provider aws \
    --plugins velero/velero-plugin-for-aws:v1.0.0 \
    --bucket velero \
    --secret-file ./credentials-velero \
    --use-volume-snapshots=false \
    --backup-location-config region=minio,s3ForcePathStyle="true",s3Url=http://$NODEIP:$NODEPORT

### Image修正(Private Repository指定に変更)
kubectl patch -n velero deployment velero -p '{"spec":{"template":{"spec":{"containers":[{"name":"velero","image":"HARBOR-FQDN/library/velero:v1.2.0"}]}}}}'  
kubectl patch -n velero deployment velero -p '{"spec":{"template":{"spec":{"initContainers":[{"name":"velero-plugin-for-aws","image":"HARBOR-FQDN/library/velero-plugin-for-aws:v1.0.0"}]}}}}'  
kubectl logs deployment/velero -n velero

### Test用Pod作成
kubectl run busybox-curl --image=yauritux/busybox-curl --restart=Never --labels=app=busybox -- sleep 3600  
kubectl get pods

### Velero Backup
velero backup create busybox-backup --selector app=busybox  
velero backup describe busybox-backup

### Test用Pod削除
kubectl delete pod busybox-curl  
kubectl get pods

### Velero Restore
velero restore create --from-backup busybox-backup  
velero restore get  
kubectl get pods