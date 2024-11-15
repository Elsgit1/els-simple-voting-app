1. Create clusterrole with associated RBAC permissions, create service account for traefik, and a clusterrolebinding:
     kubectl apply -f traefik_clusterole.yaml

2. Install traefik using helm, while using the traefik_values.yaml to customize it:
     helm repo add traefik https://helm.traefik.io/traefik
     helm repo update

     helm install traefik traefik/traefik -f traefik_values.yaml -n traefik --create-namespace

3. Config for https traffic, needa install cert-manager using helm:
     helm repo add jetstack https://charts.jetstack.io
     helm repo update

     helm install cert-manager jetstack/cert-manager \
       --namespace cert-manager \
       --create-namespace \
       --version v1.15.1 \
       --set installCRDs=true

4. We now need a cluster issuer. Do: 
     kubectl apply cert-clusterissuer.yaml

5. We need to create certificate resource for the domain (will have to create multiple cert resources if using subdomains for routing to diff svc):
      Kubectl apply -f certificate.yaml 

   Note: has to be created in same namespace where traefik is installed.

6. Create traefik ingress resources/routes:
      kubectl apply traefik_routes.yaml

   Note: has to be created in same namespace where traefik is installed.
     

7. Make sure your DNS config has an A-record for "yourdomain.com" pointing to the public IP of the loadbalancer that's created for traefik ingress controler.



