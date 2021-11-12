## Question - Network Policy

### K8s Docs

[Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/)

Create a network policy called "np-restriction" to the pod "nginx-pod" in the namespace "moon"

Only allow pods to connect to the pod "nginx-pod":

- Pods in the namespace "hello" (kubectl get ns --show-labels)
- Pods with label "app:frontend" in any namespace

#### 1 - Apply network policy based on conditions

```sh

kubectl get ns hello --show-labels >>> Namespace hello has label "ns: test"

vi /root/netpol.yaml

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np-restriction
  namespace: moon
spec:
  podSelector:
    matchLabels:
      run: nginx-pod
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          ns: test
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 6379

kubectl apply -f /root/netpol.yaml

```
