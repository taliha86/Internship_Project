# Commands to Setup Or start  the Project

```
# start minikube
minikube  start
# Verify namespace
kubectl get ns
# Verify running pods and services
kubectl get pods -n demo-apps
kubectl get svc -n demo-apps
kubectl get pods -n monitoring
kubectl get svc -n demo-apps
```

## Command to access grafana in browser

```

kubectl port-forward svc/monitoring-grafana -n monitoring 3000:80

```

## Command to access prometheus in browser

```

kubectl port-forward svc/monitoring-kube-prometheus-prometheus -n monitoring 9090:9090

```
## Command to generate traffic and Errors

```
# for error:
while true; do curl $(minikube service nginx-service -n demo-apps --url)/wrongpage

# for traffic spike (total request rate):
while true; do
curl $(minikube service nginx-service -n demo-apps --url)
done

```
###command to access Mtail
```
kubectl port-forward svc/apache-mtail -n demo-apps 3903
```
