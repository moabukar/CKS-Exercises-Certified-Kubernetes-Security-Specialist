### Question - Audit

### K8s Docs

https://kubernetes.io/docs/tasks/debug-application-cluster/audit/

Question: Enable auditing in this kubernetes cluster. Create a new audit policy file that will only log events based on the details:

- Namespace: test
- Level: metadata
- Operations: delete & update
- Resources: pods
- Log Path: /var/log/test-audit.log
- Audit file location: /etc/kubernetes/test-audit.yaml
- Maximum days to keep the logs: 20


#### 1 - Create the audit file

```sh

Create audit file in /etc/kubernetes/test-audit.yaml

apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Metadata
  namespace: ["test"]
  verb: ["delete","update"]
  resources:
  - group: ""
    resource: ["pods"]

```

#### 2 - Enable auditing in the kube-api server

```sh

- --audit-policy-file=/etc/kubernetes/test-audit.yaml
- --audit-log-path=/var/log/test-audit.log
- --audit-log-maxage=20

```

#### 3 - Add the volumes and volume mounts in the kube-api server

```sh

volumes:
  - name: audit
    hostPath:
      path: /etc/kubernetes/test-audit.yaml
      type: File
  - name: audit-log
    hostPath:
      path: /var/log/test-audit.log
      type: FileOrCreate
volumeMounts:
  - mountPath: /etc/kubernetes/test-audit.yaml
    name: audit
    readOnly: true
  - mountPath: /var/log/test-audit.log
    name: audit-log
    readOnly: false


- After exiting the kube-api server manifest, give it a moment for the server to restart and for changes to take effect.

```



