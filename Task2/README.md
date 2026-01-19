Активируйте metrics-server.
```
minikube start -p mk-docker --driver=docker --addons=metrics-server

kubectl -n kube-system patch deployment metrics-server \
  --type='json' \
  -p='[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'


kubectl -n kube-system patch deployment metrics-server \
  --type='json' \
  -p='[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname"}]'
  
kubectl rollout status -n kube-system deployment/metrics-server

kubectl apply -f scaletestapp-deployment.yaml
kubectl apply -f scaletestapp-hpa.yaml
kubectl port-forward deploy/scaletestapp 8080:8080


kubectl describe pod -n default -l app=scaletestapp
kubectl describe hpa scaletestapp-hpa
kubectl port-forward deploy/scaletestapp 8080:8080
```

Список неймспейсов
`kubectl get namespaces`

Список подов
```
kubectl get pods -A
kubectl get pods --field-selector=status.phase=Running
```

Убедится что metrics активен
```
kubectl get deployment metrics-server -n kube-system
kubectl get pods -n kube-system | grep metrics-server
kubectl get apiservices | grep metrics
```
Проверить что метрики отдаются
`kubectl top nodes`

Проверить что лимит применился
```
kubectl describe pod -l app=scaletestapp | grep -A3 -E "Limits|Requests"
```

Check 
```
kubectl get hpa
kubectl describe hpa scaletestapp-hpa
kubectl get hpa scaletestapp-hpa
kubectl top pods -l app=scaletestapp  
```

Get log 
```
kubectl logs -n kube-system deployment/metrics-server --tail=100
```

Stop & remove the existing node
```
minikube stop
minikube delete
minikube -p minikube delete --all --purge
docker system prune -f
```

```
curl 127.0.0.1:8080
minikube dashboard -p mk-docker
```