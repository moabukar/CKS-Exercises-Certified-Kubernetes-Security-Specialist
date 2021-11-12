### Question - Network Policy

### K8s Docs

[Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

Create a network policy named all-deny and it should deny all ingress and egress traffic.

#### 1 - Enable PSP in kube-api server

```sh

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: all-deny
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress

```
