### Question - Seccomp

Create a new pod called "nginx-auditing" in the "alpha" namespace using the nginx image. Secure the syscalls that this pod uses by using the local seccomp profile in the pods security context. The auditing.json should be at the "~/" directory.

<details close>
<summary> Solution</summary>
<br>
### Solution

- [Seccomp K8s docs](https://kubernetes.io/docs/tutorials/clusters/seccomp/)

#### 1 - Copy the seccomp profile to the appropriate directory

```sh

cp ~/auditing.json /var/lib/kubelet/seccomp/profiles

```

#### 2 - Change the seccomp profile by adding the below argument in the kubelet config file

```sh
Add 'seccompDefault: true' to /var/lib/kubelet/config.yaml

streamingConnectionIdleTimeout: 0s
syncFrequency: 0s
volumeStatsAggPeriod: 0s
seccompDefault: true

```

#### 3 - Restart Kubelet:

```sh

sudo systemctl restart kubelet

```

#### 4 - Create the pod using the seccomp profile

```sh

vi ~/seccomp-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx-auditing
spec:
  containers:
  - image: nginx
    name: nginx
  securityContext: ## add Security context and apply seccompProfile
    seccompProfile:
      type: Localhost 
      localhostProfile: profiles/auditing.json ## as its localhost, profile location should be here

```

#### 5 - Apply the pod

```sh

kubectl apply -f ~/seccomp-pod.yaml

```
</details>
