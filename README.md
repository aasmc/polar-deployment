# Polar Deployment

### Useful Commands:

```bash
./gradlew bootBuildImage \
--imageName ghcr.io/<your_github_username>/catalog-service \
--publishImage \
-PregistryUrl=ghcr.io \
-PregistryUsername=<your_github_username> \
-PregistryToken=<your_github_token>
```

```bash  
minikube start --cpus 2 --memory 4g --driver docker --profile polar
```

```bash
kubectl api-resources
```

```bash
minikube image load catalog-service --profile polar
```

```bash
kubectl get all -l app=catalog-service
```

```bash
kubectl logs deployment/catalog-service
```

```bash
kubeval --strict -d k8s 
```

```bash
minikube addons enable ingress --profile polar
```

```bash
kubectl get all -n ingress-nginx
```

