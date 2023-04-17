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

Run bash in keycloak container
```bash
docker exec -it polar-keycloak bash
```

Navigate to the folder where the Keycloak Admin CLI scripts are located
```bash
cd /opt/keycloak/bin
```

Start an authenticated session before running any other commands
```bash
$ ./kcadm.sh config credentials \
    --server http://localhost:8080 \
    --realm master \
    --user user \
    --password password
```

Create a new security realm where all the policies associated with Polar Bookshop will be stored

```bash
./kcadm.sh create realms -s realm=PolarBookshop -s enabled=true
```

Create two roles in the PolarBookshop realm: employee (can add new books to the catalog, 
modify existing ones and delete them), customer (can browse books and purchase them)

```bash
./kcadm.sh create roles -r PolarBookshop -s name=employee
./kcadm.sh create roles -r PolarBookshop -s name=customer
```

Create two users for testing purposes

```bash
./kcadm.sh create users -r PolarBookshop \
    -s username=isabelle \
    -s firstName=Isabelle \
    -s lastName=Dahl \
    -s enabled=true
    
./kcadm.sh add-roles -r PolarBookshop \
     --uusername isabelle \
     --rolename employee \
     --rolename customer
     
./kcadm.sh create users -r PolarBookshop \
    -s username=bjorn \
    -s firstName=Bjorn \
    -s lastName=Vinterberg \
    -s enabled=true
    
./kcadm.sh add-roles -r PolarBookshop \
     --uusername bjorn \
     --rolename employee 

./kcadm.sh set-password -r PolarBookshop \
   --username isabelle --new-password password

./kcadm.sh set-password -r PolarBookshop \
   --username bjorn --new-password password
```

Register Edge Service as an OAuth2 Client in the PolarBookshop realm in Keycloak
```bash
./kcadm.sh create clients -r PolarBookshop \
    -s clientId=edge-service \
    -s enabled=true \
    -s publicClient=false \
    -s secret=polar-keycloak-secret \
    -s 'redirectUris=["http://localhost:9000","http://localhost:9000/login/oauth2/code/*"]'
```

To run all the services on a local k8s cluster we need to map polar-keycloak host name
to the IP address of the cluster:

```bash
echo "<ip-address> polar-keycloak" | sudo tee -a /etc/hosts 
```

Get the IP address of the cluster
```bash
minikube ip --profile polar
```

Tunnel traffic on Windows or Mac

```bash
minikube tunnel --profile polar
```

Use Kustomize CLI change the image to be used in the cluster. 
This command will automatically update the kustomization.yml file with the new configuration.
```bash
kustomize edit set image catalog-service=ghcr.io/aasmc/catalog-service:latest
```