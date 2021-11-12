
- Secure a cluster using a Network Policy
  <details>

  ```
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: allow-access
  spec:
    podSelector:
      matchLabels:
        run: pod1
    policyTypes:
    - Ingress
    ingress:
    - from:
      - namespaceSelector:
          matchLabels:
            env: redis-1
      - podSelector:
          matchLabels:
            env: env-2
      ports:
      - protocol: TCP
        port: 80
  ```
  </details>
