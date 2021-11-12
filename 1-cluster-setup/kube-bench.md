#### 1 - Installing kube-bench:
```sh
curl -L https://github.com/aquasecurity/kube-bench/releases/download/v0.4.0/kube-bench_0.4.0_linux_amd64.tar.gz -o kube-bench_0.4.0_linux_amd64.tar.gz

tar -xvf kube-bench_0.4.0_linux_amd64.tar.gz
```
The kube-bench version used here is v0.4.0 
Make sure to change the version depending on your needs.

#### 2 - Running a kube-bench test

```sh
./kube-bench --config-dir `pwd`/cfg --config `pwd`/cfg/config.yaml
```
#### 3 - check results and fix any failiures

kube-bench will analyse 5 main configurations and policies as listed below:

```sh
<span style="color:blue">[INFO]</span> 1 Master Node Security Configuration
<span style="color:blue">[INFO]</span> 2 Etcd Node Configuration
<span style="color:blue">[INFO]</span> 3 Control Plane Configuration
<span style="color:blue">[INFO]</span> 4 Worker Node Security Configuration
<span style="color:blue">[INFO]</span> 5 MKubernetes Policies
```

For each section it will give you a summary, breakdown and remedies similar to this:

```sh
<span style="color:blue">[INFO]</span> 3 Control Plane Configuration
<span style="color:blue">[INFO]</span> 3.1 Authentication and Authorization
<span style="color:yellow">[WARN]</span> 3.1.1 Client certificate authentication should not be used for users (Manual)
<span style="color:blue">[INFO]</span> 3.2 Logging
<span style="color:yellow">[WARN]</span> 3.2.1 Ensure that a minimal audit policy is created (Manual)
<span style="color:yellow">[WARN]</span> 3.2.2 Ensure that the audit policy covers key security concerns (Manual)

<span style="color:yellow">== Remediations ==</span>
3.1.1 Alternative mechanisms provided by Kubernetes such as the use of OIDC should be
implemented in place of client certificates.

3.2.1 Create an audit policy file for your cluster.

3.2.2 Consider modification of the audit policy in use on the cluster to include these items, at a
minimum.


<span style="color:yellow">== Summary ==</span>
0 checks PASS
0 checks FAIL
3 checks WARN
0 checks INFO
```