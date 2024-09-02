# jitsi

Enable the `microk8s` features we need:
```
microk8s enable cert-manager ingress dns hostpath-storage
```

###Notes
- `cert-manager` handles TLS, and `ingress` is an nginx-based proxy to handle the TLS connections.
- `hostpath-storage` is needed to be able to start jitsi calls (see here: https://github.com/jitsi-contrib/jitsi-helm/issues/63)


## yaml files
First, the self-signed TLS cert setup. We need an "Issuer", a "ClusterIssuer", a CA certificate, and a service certificate.
- `microk8s kubectl apply -f issuer.yaml`
- `microk8s kubectl apply -f cluster-issuer.yaml`
- `microk8s kubectl apply -f ca.yaml`
- `microk8s kubectl apply -f cert.yaml`

Now we install the jitsi service using helm:
- `helm repo add jitsi https://jitsi-contrib.github.io/jitsi-helm/`
- `helm install <name> jitsi/jitsi-meet -f jitsi.yaml`

Starting kubeinvaders for crash injections:
- `helm install -n kubeinvaders kubeinvaders ./kubeinvaders/helm-charts/kubeinvaders --set ingress.enabled=false --set route_host=localhost:8080 --set config.insecure='"true"' --set deployment.image.tag="v1.9.6" --set-string config.target_namespace="jitsi\,default"`
