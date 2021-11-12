## Question - ImagePolicyWebhook
### K8s Docs

[Kube-Admission Controllers](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/)

We are required to deploy an ImagePolicyWebhook admission controller to secure the deployments that are made on our cluster by ensuring the below:

- Update and fix the issue on the /etc/kubernetes/pki/admission_configuration.yaml which will be used by ImagePolicyWebhook
- Ensure that the policy is set to implicit deny.
- Ensure the plugin is on the kube-api server

#### 1 - Update and fix the issue on the admission config

```sh

apiVersion: apiserver.config.k8s.io/v1
kind: AdmissionConfiguration
plugins:
- name: ImagePolicyWebhook
  configuration:
    imagePolicy:
      kubeConfigFile: /etc/kubernetes/pki/admission_kube_config.yaml
      allowTTL: 50
      denyTTL: 50
      retryBackoff: 500
      defaultAllow: false

```

#### 2 - Update the kube-api server

```sh

- --enable-admission-plugins=NodeRestriction,ImagePolicyWebhook
- --admission-control-config-file=/etc/kubernetes/pki/admission_configuration.yaml

The API server will automatically restart and pickup the configuration.

```
