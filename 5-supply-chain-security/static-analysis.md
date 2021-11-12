## Static Analysis

### Option 1 - kubesec
#### Documentation

https://github.com/controlplaneio/kubesec

#### Installation

https://github.com/controlplaneio/kubesec/releases

#### Test manifest file

```sh

vi private-pod.yaml

```

```sh

apiVersion: v1
kind: Pod
metadata:
  name: privileged
spec:
  containers:
  - image: nginx
    name: test-pod
    securityContext:
      privileged: true

```

#### Performing static analysis

```sh

kubesec scan -f private-pod.yaml

```

### Option 2  - checkov

#### Documentation:

https://github.com/bridgecrewio/checkov

#### Installation

```sh

apt install python3-pip
pip3 install checkov

```

#### Test manifest file

```sh

vi private-pod.yaml

```

```sh

apiVersion: v1
kind: Pod
metadata:
  name: privileged
spec:
  containers:
  - image: nginx
    name: test-pod
    securityContext:
      privileged: true

```

#### Performing static analysis

```sh

checkov -f private-pod.yaml

```
