### Question - Seccomp

Create a new pod called "nginx-auditing" in the "alpha" namespace using the nginx image. Secure the syscalls that this pod uses by using the local seccomp profile in the pods security context. The auditing.json should be at the /root directory.

#### 1 - Copy the seccomp profile to the appropriate directory

```sh

cp /root/auditing.json /var/lib/kubelet/seccomp/profiles

```

#### 2 - Create the pod using the seccomp profile

```sh

vi /root/seccomp-pod.yaml

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
  securityContext:
    seccompProfile:
      type: Localhost
      localhostProfile: profiles/auditing.json

```

#### 3 - Apply the pod

```sh

kubectl apply -f /root/seccomp-pod.yaml

```
