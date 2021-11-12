### Question - Audit

### K8s Docs

https://kubernetes.io/docs/tasks/debug-application-cluster/audit/

Question: Enable auditing in this kubernetes cluster. Create a new policy file that will only log events based on the below specifications:

Namespace: test
Level: metadata
Operations: delete
Resources: secrets
Log Path: /var/log/test-secrets.log
Audit file location: /etc/kubernetes/test-audit.yaml
Maximum days to keep the logs: 20


- "authoirsation mode is set as Always allowed"
- "Kernel defaults are not protected"

Make changes to the /var/lib/kubelet/config.yaml

#### 1 - Update the kubelet config

```sh



```