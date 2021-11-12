## Question - ImagePolicyWebhook

### K8s Docs

[Pod Security Policy](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)

Create the PSP with the following conditions:
- PSP name : pod-psp
- Not allow privleged pods
- Allow volumes to mount on pod: secret, configMap
- seLinux, runAsUser, fsGroup: RunAsAny

#### 1 - Enable PSP in kube-api server

```sh

  - --enable-admission-plugins=NodeRestriction,PodSecurityPolicy

```

#### 2 - Create the PSP

```sh

vi psp.yaml

apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: pod-psp
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - configMap
  - secret

kubectl apply -f psp.yaml

```

#### 3 - Apply PSP on pod

```sh

vi /root/pod.yaml

apiVersion: v1
kind: Pod
metadata:
    name: psp-pod
spec:
    containers:
    - name: app
      image: ubuntu
      command: ["sleep" , "3600"]
    securityContext:
      privileged: False
      runAsUser: 0
    volumes:
    - name: data-volume
      hostPath:
        path: '/data'
        type: Directory

kubectl apply -f /root/pod.yaml

```
