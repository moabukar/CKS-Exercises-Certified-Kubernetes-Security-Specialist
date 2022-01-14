## [Cluster setup (10%)](../1-cluster-setup/)

- Using network policies to restrict cluster level access
- Using CIS benchmark to review security configuration of k8s components (kube-api, kubelet, etcd, kubedns) — Kube-bench
- Properly setting up ingress objects with security controls
- Protecting node metadata and endpoints
- Minimising the use of, and access to, GUI elements
- Verify platform binaries before deployment — binary verification using sha512 hash

## [Cluster Hardening (15%)](../2-cluster-hardening/)

- Restricting access to the Kubernetes API
- Using RBAC (role-based access controls) to minimise exposure
- Using service account with minimal permissions and disabling defaults — opting out of automounting credentials for service accounts
- Keeping up to date with the latest Kubernetes versions

## [System Hardening (15%)](../3-system-hardening/)

- Minimising footprint of host OS to reduce attack surface
- Minimising IAM roles: Principle of least privilege — Authentication and Authorisation
- Minimise external access to the network — denying external access to outside the cluster
- Using kernel hardening tools — such as seccomp and AppArmor correctly

## [Minimise Microservice Vulnerabilities (20%)](../4-minimise-microservice-vulnerabilities/)

- Setting up appropriate OS level security domains like PSP, OPA and security contexts
- Managing K8s secrets
- Use container runtime sandboxes in multi-tenant environments like gVisor and kata-containers.
- Implementation of pod-to-pod encryption by using mTLS configurations

## [Supply Chain Security (20%)](../5-supply-chain-security/)

- Minimise base image footprint — distroless, alpine and an image relevant to your build as well as following best practices when creating containers
- Securing your supply chain by signing and validating images and a whitelist of allowed image registries — ImagePolicyWebhook admission controller.
- Scan images for known vulnerabilities — Aquasec Trivy

## [Monitoring, Logging and Runtime security (20%)](../6-monitoring-logging-runtime-security/)

- Performing analytics of syscall processes at host and container level to detect malicious activities — [Falco](https://falco.org/docs/)
- Detect threats within physical infrastructure, apps, networks, data, users and workloads
- Detect all phase of attack regardless of where it happens and its spread
- Performing deep analytical investigation and identification of bad actors within environment — [Sysdig](https://sysdig.com/)
- Ensuring immutability of containers at runtime
- Audit logs to monitor access