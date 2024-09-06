# Kong

## Brief description
Kong in hybrid mode has a control plane (that has a database) and a data plane (that exposes the proxy), they communicate to each other through a secure way using TLS certificates. The controlplane controls the database, Kong Manager and admin API.

For using Kubernetes gateway API specification in order to control the gateway we need to deploy the KIC (Kong Ingress Controller), configure k8s gtw api crds and create a new gatewayclass.

## Installation

### Install kong
```bash
helm upgrade kong-cp kong/kong --install -nkong -f kong-cp-values.yaml # Control plane
helm upgrade kong-dp kong/kong --install -nkong -f kong-dp-values.yaml # Data plane
```

### Install gateway
```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.1.0/standard-install.yaml # Kubernetes gtw CRDs
kubectl apply -f kong-gtw.yaml
helm install kong kong/ingress -n kong -f kong-ingressController.yaml # Install kong ingressController
```