<img src="img/microk8s-logo.png" height="100">   <img src="img/kubernetes-logo.png" height="100">    <img src="img/kube-bench-logo.png" height="105">

# MicroK8s & Kubernetes security benchmark from CIS

## Goal & Deliverables

![workflow badge](https://github.com/didier-durand/microk8s-kube-bench/workflows/MicroK8s%20kube-bench/badge.svg)

MicroK8s analysed for CIS benchmark with kube-bench. 

This repository implements a 100% automated workflow (via [microk8s-kube-bench.yml](.github/workflows/microk8s-kube-bench.yml) + [microk8s-kube-bench.sh](sh/microk8s-kube-bench.sh)) providing the installation of Microk8s on Ubuntu (run as a Github CI /CD worker). Kube-bench is then deployed and executed to obtain the analysis of the configuration of this Kubernetes cluster.

Last execution report on Github CI/CD is appended below. This workflow is scheduled for daily execution via cron directive in 
[microk8s-kube-bench.yml](.github/workflows/microk8s-kube-bench.yml)) : it can then check new snaps (see below) of MicroK8s as they get published. 

All suggestions for improvements or extensions are welcome. Same for pull requests!

## MicroK8s

[Microk8s](https://github.com/ubuntu/microk8s) by Canonical is single-package, fully conformant and lightweight Kubernetes distribution that works 
on numerous flavours of Linux. It is aimed at developer workstations, IoT, Edge & CI/CD. Simple to manage, this pure upstream distribution has 
same-day tracking for new releases, patches generated by root project. The [Snap package manager](https://en.wikipedia.org/wiki/Snap_(package_manager)) 
takes care of corresponding automated updates when new upstream code gets pushed.

Canonical additionally provides various preconfigured [standard K8s add-ons](https://microk8s.io/docs/addons) on top of the raw distribution: 
dashboard, istio, knative, metallb, cilium, kubeflow, etc.  They make MicroK8s quite suitable for advanced tests on a laptop. It is also a very 
easy way to [get started](https://microk8s.io/tutorials) with K8s on an autonomous / personal system.

 
## Kube-bench

As [per Wikipedia](https://en.wikipedia.org/wiki/Center_for_Internet_Security): *"The Center for Internet Security (CIS) is a 501(c)(3) nonprofit 
organization, formed in October, 2000. Its mission is to "identify, develop, validate, promote, and sustain best practice 
solutions for cyber defense and build and lead communities to enable an environment of trust in cyberspace".The organization is headquartered in 
East Greenbush, New York, with members including large corporations, government agencies, and academic institutions."*

[kube-bench](https://github.com/aquasecurity/kube-bench) by [Aqua Security](https://www.aquasec.com/) is a Go application that analyses how securely 
Kubernetes is deployed by running the checks documented in the [CIS Kubernetes Benchmark](https://www.cisecurity.org/benchmark/kubernetes/).
Tests are configured with YAML files, making this tool easy to update as test specifications evolve.

The numbered items (like 1.2.3) found in execution report below correspond to the various points being defined and commented with this same number 
in the [official documentation](https://www.cisecurity.org/benchmark/kubernetes/) of the benchmark.

Basically, the analyis is executed via a [Yaml manifest](https://github.com/aquasecurity/kube-bench/blob/master/job.yaml) defining a [Kubernetes Job](https://kubernetes.io/docs/concepts/workloads/controllers/job/) deployed on the cluster. This job triggers the execution of
[aquasec/kube-bench:latest](https://hub.docker.com/r/aquasec/kube-bench) container image pulled from Docker Hub.

[This article](https://medium.com/better-programming/how-to-harden-your-kubernetes-cluster-with-kube-bench-fae71eb24d8) delivers deep and interesting 
insights on the benchmark.

## Fork and setup

If you want to reuse this repository, just fork it in your account. You can right away execute this workflow by using the button defined in the 
[workflow yaml](.github/workflows/microk8s-kube-bench.yml) via *'workflow_dispatch'* directive. No commit to change code needed: just run!

<img src="img/microk8s-kube-bench-workflow-button.jpg" height="250">

## Last execution report

```
execution date: Fri May  6 02:46:21 UTC 2022
 
microk8s snap version: microk8s  v1.24.0        3220   latest/edge    canonical*  classic
 
W0506 02:46:03.225735    5698 util.go:312] 
Unable to find the programs kubectl or kubelet in the PATH.
These programs are used to determine which version of Kubernetes is running.
Make sure the /usr/local/mount-from-host/bin directory is mapped to the container,
either in the job.yaml file, or Docker command.

For job.yaml:
...
- name: usr-bin
  mountPath: /usr/local/mount-from-host/bin
...

For docker command:
   docker -v $(which kubectl):/usr/local/mount-from-host/bin/kubectl ....

Alternatively, you can specify the version with --version
   kube-bench --version <VERSION> ...
W0506 02:46:14.166874    5698 util.go:312] 
Unable to find the programs kubectl or kubelet in the PATH.
These programs are used to determine which version of Kubernetes is running.
Make sure the /usr/local/mount-from-host/bin directory is mapped to the container,
either in the job.yaml file, or Docker command.

For job.yaml:
...
- name: usr-bin
  mountPath: /usr/local/mount-from-host/bin
...

For docker command:
   docker -v $(which kubectl):/usr/local/mount-from-host/bin/kubectl ....

Alternatively, you can specify the version with --version
   kube-bench --version <VERSION> ...
[INFO] 4 Worker Node Security Configuration
[INFO] 4.1 Worker Node Configuration Files
[PASS] 4.1.1 Ensure that the kubelet service file permissions are set to 644 or more restrictive (Automated)
[PASS] 4.1.2 Ensure that the kubelet service file ownership is set to root:root (Automated)
[PASS] 4.1.3 If proxy kubeconfig file exists ensure permissions are set to 644 or more restrictive (Manual)
[PASS] 4.1.4 Ensure that the proxy kubeconfig file ownership is set to root:root (Manual)
[FAIL] 4.1.5 Ensure that the --kubeconfig kubelet.conf file permissions are set to 644 or more restrictive (Automated)
[WARN] 4.1.6 Ensure that the --kubeconfig kubelet.conf file ownership is set to root:root (Manual)
[WARN] 4.1.7 Ensure that the certificate authorities file permissions are set to 644 or more restrictive (Manual)
[WARN] 4.1.8 Ensure that the client certificate authorities file ownership is set to root:root (Manual)
[PASS] 4.1.9 Ensure that the kubelet --config configuration file has permissions set to 644 or more restrictive (Automated)
[PASS] 4.1.10 Ensure that the kubelet --config configuration file ownership is set to root:root (Automated)
[INFO] 4.2 Kubelet
[FAIL] 4.2.1 Ensure that the anonymous-auth argument is set to false (Automated)
[FAIL] 4.2.2 Ensure that the --authorization-mode argument is not set to AlwaysAllow (Automated)
[FAIL] 4.2.3 Ensure that the --client-ca-file argument is set as appropriate (Automated)
[WARN] 4.2.4 Ensure that the --read-only-port argument is set to 0 (Manual)
[WARN] 4.2.5 Ensure that the --streaming-connection-idle-timeout argument is not set to 0 (Manual)
[FAIL] 4.2.6 Ensure that the --protect-kernel-defaults argument is set to true (Automated)
[FAIL] 4.2.7 Ensure that the --make-iptables-util-chains argument is set to true (Automated)
[WARN] 4.2.8 Ensure that the --hostname-override argument is not set (Manual)
[WARN] 4.2.9 Ensure that the --event-qps argument is set to 0 or a level which ensures appropriate event capture (Manual)
[WARN] 4.2.10 Ensure that the --tls-cert-file and --tls-private-key-file arguments are set as appropriate (Manual)
[WARN] 4.2.11 Ensure that the --rotate-certificates argument is not set to false (Manual)
[WARN] 4.2.12 Verify that the RotateKubeletServerCertificate argument is set to true (Manual)
[WARN] 4.2.13 Ensure that the Kubelet only makes use of Strong Cryptographic Ciphers (Manual)

== Remediations node ==
4.1.5 Run the below command (based on the file location on your system) on the each worker node.
For example,
chmod 644 /etc/kubernetes/kubelet.conf

4.1.6 Run the below command (based on the file location on your system) on the each worker node.
For example,
chown root:root /etc/kubernetes/kubelet.conf

4.1.7 Run the following command to modify the file permissions of the
--client-ca-file chmod 644 <filename>

4.1.8 Run the following command to modify the ownership of the --client-ca-file.
chown root:root <filename>

4.2.1 If using a Kubelet config file, edit the file to set authentication: anonymous: enabled to
false.
If using executable arguments, edit the kubelet service file
/etc/systemd/system/snap.microk8s.daemon-kubelet.service on each worker node and
set the below parameter in KUBELET_SYSTEM_PODS_ARGS variable.
--anonymous-auth=false
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.2 If using a Kubelet config file, edit the file to set authorization: mode to Webhook. If
using executable arguments, edit the kubelet service file
/etc/systemd/system/snap.microk8s.daemon-kubelet.service on each worker node and
set the below parameter in KUBELET_AUTHZ_ARGS variable.
--authorization-mode=Webhook
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.3 If using a Kubelet config file, edit the file to set authentication: x509: clientCAFile to
the location of the client CA file.
If using command line arguments, edit the kubelet service file
/etc/systemd/system/snap.microk8s.daemon-kubelet.service on each worker node and
set the below parameter in KUBELET_AUTHZ_ARGS variable.
--client-ca-file=<path/to/client-ca-file>
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.4 audit test did not run: failed to run: "/bin/ps -fC $kubeletbin", output: "error: list of command names must follow -C\n\nUsage:\n ps [options]\n\n Try 'ps --help <simple|list|output|threads|misc|all>'\n  or 'ps --help <s|l|o|t|m|a>'\n for additional help text.\n\nFor more details see ps(1).\n", error: exit status 1
4.2.5 audit test did not run: failed to run: "/bin/ps -fC $kubeletbin", output: "error: list of command names must follow -C\n\nUsage:\n ps [options]\n\n Try 'ps --help <simple|list|output|threads|misc|all>'\n  or 'ps --help <s|l|o|t|m|a>'\n for additional help text.\n\nFor more details see ps(1).\n", error: exit status 1
4.2.6 If using a Kubelet config file, edit the file to set protectKernelDefaults: true.
If using command line arguments, edit the kubelet service file
/etc/systemd/system/snap.microk8s.daemon-kubelet.service on each worker node and
set the below parameter in KUBELET_SYSTEM_PODS_ARGS variable.
--protect-kernel-defaults=true
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.7 If using a Kubelet config file, edit the file to set makeIPTablesUtilChains: true.
If using command line arguments, edit the kubelet service file
/etc/systemd/system/snap.microk8s.daemon-kubelet.service on each worker node and
remove the --make-iptables-util-chains argument from the
KUBELET_SYSTEM_PODS_ARGS variable.
Based on your system, restart the kubelet service. For example:
systemctl daemon-reload
systemctl restart kubelet.service

4.2.8 audit test did not run: failed to run: "/bin/ps -fC $kubeletbin", output: "error: list of command names must follow -C\n\nUsage:\n ps [options]\n\n Try 'ps --help <simple|list|output|threads|misc|all>'\n  or 'ps --help <s|l|o|t|m|a>'\n for additional help text.\n\nFor more details see ps(1).\n", error: exit status 1
4.2.9 audit test did not run: failed to run: "/bin/ps -fC $kubeletbin", output: "error: list of command names must follow -C\n\nUsage:\n ps [options]\n\n Try 'ps --help <simple|list|output|threads|misc|all>'\n  or 'ps --help <s|l|o|t|m|a>'\n for additional help text.\n\nFor more details see ps(1).\n", error: exit status 1
4.2.10 audit test did not run: failed to run: "/bin/ps -fC $kubeletbin", output: "error: list of command names must follow -C\n\nUsage:\n ps [options]\n\n Try 'ps --help <simple|list|output|threads|misc|all>'\n  or 'ps --help <s|l|o|t|m|a>'\n for additional help text.\n\nFor more details see ps(1).\n", error: exit status 1
4.2.11 audit test did not run: failed to run: "/bin/ps -fC $kubeletbin", output: "error: list of command names must follow -C\n\nUsage:\n ps [options]\n\n Try 'ps --help <simple|list|output|threads|misc|all>'\n  or 'ps --help <s|l|o|t|m|a>'\n for additional help text.\n\nFor more details see ps(1).\n", error: exit status 1
4.2.12 audit test did not run: failed to run: "/bin/ps -fC $kubeletbin", output: "error: list of command names must follow -C\n\nUsage:\n ps [options]\n\n Try 'ps --help <simple|list|output|threads|misc|all>'\n  or 'ps --help <s|l|o|t|m|a>'\n for additional help text.\n\nFor more details see ps(1).\n", error: exit status 1
4.2.13 audit test did not run: failed to run: "/bin/ps -fC $kubeletbin", output: "error: list of command names must follow -C\n\nUsage:\n ps [options]\n\n Try 'ps --help <simple|list|output|threads|misc|all>'\n  or 'ps --help <s|l|o|t|m|a>'\n for additional help text.\n\nFor more details see ps(1).\n", error: exit status 1

== Summary node ==
6 checks PASS
6 checks FAIL
11 checks WARN
0 checks INFO

[INFO] 5 Kubernetes Policies
[INFO] 5.1 RBAC and Service Accounts
[WARN] 5.1.1 Ensure that the cluster-admin role is only used where required (Manual)
[WARN] 5.1.2 Minimize access to secrets (Manual)
[WARN] 5.1.3 Minimize wildcard use in Roles and ClusterRoles (Manual)
[WARN] 5.1.4 Minimize access to create pods (Manual)
[WARN] 5.1.5 Ensure that default service accounts are not actively used. (Manual)
[WARN] 5.1.6 Ensure that Service Account Tokens are only mounted where necessary (Manual)
[INFO] 5.2 Pod Security Policies
[WARN] 5.2.1 Minimize the admission of privileged containers (Manual)
[WARN] 5.2.2 Minimize the admission of containers wishing to share the host process ID namespace (Manual)
[WARN] 5.2.3 Minimize the admission of containers wishing to share the host IPC namespace (Manual)
[WARN] 5.2.4 Minimize the admission of containers wishing to share the host network namespace (Manual)
[WARN] 5.2.5 Minimize the admission of containers with allowPrivilegeEscalation (Manual)
[WARN] 5.2.6 Minimize the admission of root containers (Manual)
[WARN] 5.2.7 Minimize the admission of containers with the NET_RAW capability (Manual)
[WARN] 5.2.8 Minimize the admission of containers with added capabilities (Manual)
[WARN] 5.2.9 Minimize the admission of containers with capabilities assigned (Manual)
[INFO] 5.3 Network Policies and CNI
[WARN] 5.3.1 Ensure that the CNI in use supports Network Policies (Manual)
[WARN] 5.3.2 Ensure that all Namespaces have Network Policies defined (Manual)
[INFO] 5.4 Secrets Management
[WARN] 5.4.1 Prefer using secrets as files over secrets as environment variables (Manual)
[WARN] 5.4.2 Consider external secret storage (Manual)
[INFO] 5.5 Extensible Admission Control
[WARN] 5.5.1 Configure Image Provenance using ImagePolicyWebhook admission controller (Manual)
[INFO] 5.7 General Policies
[WARN] 5.7.1 Create administrative boundaries between resources using namespaces (Manual)
[WARN] 5.7.2 Ensure that the seccomp profile is set to docker/default in your pod definitions (Manual)
[WARN] 5.7.3 Apply Security Context to Your Pods and Containers (Manual)
[WARN] 5.7.4 The default namespace should not be used (Manual)

== Remediations policies ==
5.1.1 Identify all clusterrolebindings to the cluster-admin role. Check if they are used and
if they need this role or if they could use a role with fewer privileges.
Where possible, first bind users to a lower privileged role and then remove the
clusterrolebinding to the cluster-admin role :
kubectl delete clusterrolebinding [name]

5.1.2 Where possible, remove get, list and watch access to secret objects in the cluster.

5.1.3 Where possible replace any use of wildcards in clusterroles and roles with specific
objects or actions.

5.1.4 Where possible, remove create access to pod objects in the cluster.

5.1.5 Create explicit service accounts wherever a Kubernetes workload requires specific access
to the Kubernetes API server.
Modify the configuration of each default service account to include this value
automountServiceAccountToken: false

5.1.6 Modify the definition of pods and service accounts which do not need to mount service
account tokens to disable it.

5.2.1 Create a PSP as described in the Kubernetes documentation, ensuring that
the .spec.privileged field is omitted or set to false.

5.2.2 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.hostPID field is omitted or set to false.

5.2.3 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.hostIPC field is omitted or set to false.

5.2.4 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.hostNetwork field is omitted or set to false.

5.2.5 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.allowPrivilegeEscalation field is omitted or set to false.

5.2.6 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.runAsUser.rule is set to either MustRunAsNonRoot or MustRunAs with the range of
UIDs not including 0.

5.2.7 Create a PSP as described in the Kubernetes documentation, ensuring that the
.spec.requiredDropCapabilities is set to include either NET_RAW or ALL.

5.2.8 Ensure that allowedCapabilities is not present in PSPs for the cluster unless
it is set to an empty array.

5.2.9 Review the use of capabilites in applications running on your cluster. Where a namespace
contains applicaions which do not require any Linux capabities to operate consider adding
a PSP which forbids the admission of containers which do not drop all capabilities.

5.3.1 If the CNI plugin in use does not support network policies, consideration should be given to
making use of a different plugin, or finding an alternate mechanism for restricting traffic
in the Kubernetes cluster.

5.3.2 Follow the documentation and create NetworkPolicy objects as you need them.

5.4.1 if possible, rewrite application code to read secrets from mounted secret files, rather than
from environment variables.

5.4.2 Refer to the secrets management options offered by your cloud provider or a third-party
secrets management solution.

5.5.1 Follow the Kubernetes documentation and setup image provenance.

5.7.1 Follow the documentation and create namespaces for objects in your deployment as you need
them.

5.7.2 Seccomp is an alpha feature currently. By default, all alpha features are disabled. So, you
would need to enable alpha features in the apiserver by passing "--feature-
gates=AllAlpha=true" argument.
Edit the /etc/kubernetes/apiserver file on the master node and set the KUBE_API_ARGS
parameter to "--feature-gates=AllAlpha=true"
KUBE_API_ARGS="--feature-gates=AllAlpha=true"
Based on your system, restart the kube-apiserver service. For example:
systemctl restart kube-apiserver.service
Use annotations to enable the docker/default seccomp profile in your pod definitions. An
example is as below:
apiVersion: v1
kind: Pod
metadata:
  name: trustworthy-pod
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: docker/default
spec:
  containers:
    - name: trustworthy-container
      image: sotrustworthy:latest

5.7.3 Follow the Kubernetes documentation and apply security contexts to your pods. For a
suggested list of security contexts, you may refer to the CIS Security Benchmark for Docker
Containers.

5.7.4 Ensure that namespaces are created to allow for appropriate segregation of Kubernetes
resources and that all new resources are created in a specific namespace.


== Summary policies ==
0 checks PASS
0 checks FAIL
24 checks WARN
0 checks INFO

== Summary total ==
6 checks PASS
6 checks FAIL
35 checks WARN
0 checks INFO

```
