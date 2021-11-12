### Question - Kube-bench (1)

### K8s Docs

https://kubernetes.io/docs/tasks/debug-application-cluster/audit/

You have just read the kube-bench assessment report. Fix the tests that have FAIL status for the worker node configuration.

The kube-bench report gives issues such as (or close to):

- "authoirsation mode is set as Always allowed"
- "Kernel defaults are not protected"

Make changes to the /var/lib/kubelet/config.yaml

#### 1 - Update the kubelet config

```sh

vi /var/lib/kubelet/config.yaml

change "authorization mode"

authorization:
  mode: Webhook

```