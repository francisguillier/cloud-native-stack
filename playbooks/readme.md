# Ansible Playbooks for NVIDIA Cloud Native Stack

This page describes the steps required to use Ansible to install the NVIDIA Cloud Native Stack.

### The following Ansible Playbooks are available

- [Install NVIDIA Cloud Native Stack](https://github.com/NVIDIA/cloud-native-stack/blob/26.6.0/playbooks/cns-installation.yaml)

- [Upgrade NVIDIA Cloud Native Stack ](https://github.com/NVIDIA/cloud-native-stack/blob/26.6.0/playbooks/cns-upgrade.yaml)

- [Validate NVIDIA Cloud Native Stack ](https://github.com/NVIDIA/cloud-native-stack/blob/26.6.0/playbooks/cns-validation.yaml)

- [Uninstall NVIDIA Cloud Native Stack](https://github.com/NVIDIA/cloud-native-stack/blob/26.6.0/playbooks/cns-uninstall.yaml)

## Prerequisites

- system has direct internet access
- system should have an Operating system either Ubuntu 22.04 and above or RHEL 8.7
- system has adequate internet bandWidth
- DNS server is working fine on the System
- system can access Google repo(for k8s installation)
- system has only 1 network interface configured with internet access. The IP is static and doesn't change
- UEFI secure boot is disabled
- Root file system should has at least 40GB capacity
- system has 4CPU and 8GB Memory
- At least one NVIDIA GPU attached to the system

## Systems support 
The following systems are support for Cloud Native Stack:

- You have [NVIDIA-Certified Systems](https://docs.nvidia.com/ngc/ngc-deploy-on-premises/nvidia-certified-systems/index.html) with Mellanox CX NICs for x86-64 servers 
- You have [NVIDIA Qualified Systems](https://www.nvidia.com/en-us/data-center/data-center-gpus/qualified-system-catalog/?start=0&count=50&pageNumber=1&filters=eyJmaWx0ZXJzIjpbXSwic3ViRmlsdGVycyI6eyJwcm9jZXNzb3JUeXBlIjpbIkFSTS1UaHVuZGVyWDIiLCJBUk0tQWx0cmEiXX0sImNlcnRpZmllZEZpbHRlcnMiOnt9LCJwYXlsb2FkIjpbXX0=) for arm64 servers 
  `NOTE:` For ARM systems, NVIDIA Network Operator is not supported yet. 
- You have [NVIDIA Jetson Systems](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/)

To determine if your system qualifies as an NVIDIA Certified System, review the list of NVIDIA Certified Systems [here](https://docs.nvidia.com/ngc/ngc-deploy-on-premises/nvidia-certified-systems/index.html). 

Please note that NVIDIA Cloud Native Stack is validated only on systems with the default kernel (not HWE).

### Installing the Ubuntu Operating System
These instructions require Ubuntu server please reference the [Ubuntu Server Installation Guide](https://ubuntu.com/tutorials/tutorial-install-ubuntu-server#1-overview).

### Installing JetPack for Jetson 

JetPack (the Jetson SDK) is an on-demand all-in-one package that bundles developer software for the NVIDIA® Jetson platform. There are two ways to install the JetPack 

1. Use the SDK Manager installer to flash your Jetson Developer Kit with the latest OS image, install developer tools for both host PC and Developer Kit, and install the libraries and APIs, samples, and documentation needed to jump-start your development environment.

Follow the [instructions](https://docs.nvidia.com/sdk-manager/install-with-sdkm-jetson/index.html) on how to install JetPack 5.0There are two ways to install the JetPack 

Download the SDK Manager from [here](https://developer.nvidia.com/nvidia-sdk-manager)

2. Use the SD Card Image method to download the JetPack and load the OS image to external drive. For more information, please refer [flash using SD Card method](https://developer.nvidia.com/embedded/learn/get-started-jetson-xavier-nx-devkit#prepare)

## Using the Ansible playbooks 
This section describes how to use the ansible playbooks.

### Clone the git repository

Run the below commands to clone the NVIDIA Cloud Native Stack ansible playbooks.

```
git clone -b 26.6.0 https://github.com/NVIDIA/cloud-native-stack.git
cd cloud-native-stack/playbooks
```

Update the hosts file in playbooks directory with master and worker nodes(if you have) IP's with username and password like below

```
nano hosts

[master]
10.110.16.178 ansible_ssh_user=nvidia ansible_ssh_pass=nvidipass ansible_sudo_pass=nvidiapass ansible_ssh_common_args='-o StrictHostKeyChecking=no'
[node]
10.110.16.179 ansible_ssh_user=nvidia ansible_ssh_pass=nvidiapass ansible_sudo_pass=nvidiapass ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

## Installation
Cloud Native Stack Supports below versions.

Available versions are:

- 19.1
- 18.1
- 17.1
- 17.0
- 16.1
- 16.0

Edit the `cns_version.yaml` and update the version you want to install

```
nano cns_version.yaml
```

If you want to cusomize any predefined components versions or any other custom paramenters, modify the respective CNS version values file like below and trigger the installation. 

Example:
```
$ nano cns_values_16.1.yaml
cns_version: 16.1

## MicroK8s cluster
microk8s: no
## Kubernetes Install with Kubeadm
install_k8s: yes

## Components Versions
# Container Runtime options are containerd, cri-o, cri-dockerd
container_runtime: "containerd"
containerd_version: "2.4.1"
runc_version: "1.4.0"
cni_plugins_version: "1.7.1"
containerd_max_concurrent_downloads: "5"
nvidia_container_toolkit_version: "1.20.1"
crio_version: "1.33.6"
cri_dockerd_version: "0.4.0"
k8s_version: "1.33.6"
calico_version: "3.31.3"
flannel_version: "0.25.6"
helm_version: "4.3.0"
gpu_operator_version: "26.7.1"
network_operator_version: "26.7.0"
local_path_provisioner: "0.0.31"
nfs_provisioner: "4.0.18"
metallb_version: "0.15.3"
prometheus_stack: "79.9.0"
prometheus_adapter: "5.2.0"
grafana_operator: "5.18.0"
elastic_stack: "9.2.1"

# GPU Operator Values
enable_gpu_operator: yes
confidential_computing: no
gpu_driver_version: "580.178.04"
use_open_kernel_module: no
enable_mig: no
mig_profile: all-disabled
mig_strategy: single
# To use GDS, use_open_kernel_module needs to be enabled
enable_gds: no
#Secure Boot for only Ubuntu
enable_secure_boot: no
enable_cdi: no
enable_vgpu: no
vgpu_license_server: ""
# URL of Helm repo to be added. If using NGC get this from the fetch command in the console
helm_repository: "https://helm.ngc.nvidia.com/nvidia"
# Name of the helm chart to be deployed
gpu_operator_helm_chart: nvidia/gpu-operator
## This is most likely GPU Operator Driver Registry
gpu_operator_driver_registry: "nvcr.io/nvidia"

# NGC Values
## If using a private/protected registry. NGC API Key. Leave blank for public registries
ngc_registry_password: ""
## This is most likely an NGC email
ngc_registry_email: ""
ngc_registry_username: "$oauthtoken"

# Network Operator Values
## If the Network Operator is yes then make sure enable_rdma as well yes
enable_network_operator: no
## Enable RDMA yes for NVIDIA Certification
enable_rdma: no
## Enable for MLNX-OFED Driver Deployment
deploy_ofed: no

# Prxoy Configuration
proxy: no
http_proxy: ""
https_proxy: ""

# Cloud Native Stack for Developers Values
## Enable for Cloud Native Stack Developers
cns_docker: no
## Enable For Cloud Native Stack Developers with TRD Driver
cns_nvidia_driver: no
nvidia_driver_mig: no

## Kubernetes resources
k8s_apt_key: "https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key"
k8s_gpg_key: "https://pkgs.k8s.io/core:/stable:/v1.33/rpm/repodata/repomd.xml.key"
k8s_apt_ring: "/etc/apt/keyrings/kubernetes-apt-keyring.gpg"
k8s_registry: "registry.k8s.io"





# Local Path Provisioner and NFS Provisoner as Storage option
storage: no

# Monitoring Stack Prometheus/Grafana with GPU Metrics and Elastic Logging stack
monitoring: no


# Install MetalLB
loadbalancer: no
# Example input loadbalancer_ip: "10.78.17.85/32"
loadbalancer_ip: ""
kubernetes_host_ip: ""


## Cloud Native Stack Validation
cns_validation: no

# BMC Details for Confidential Computing
bmc_ip:
bmc_username:
bmc_password:
```

Install the NVIDIA Cloud Native Stack stack by running the below command. "Skipping" in the ansible output refers to the Kubernetes cluster is up and running.
```
bash setup.sh install
```
`NOTE:` When you trigger the installation on DGX System you need to click `Enter/Return` command when you see `Restarting Services`

### Custom Configuration
By default Cloud Native Stack uses Google kubernetes apt repository, if you want to use any other kubernetes apt repository, please adjust the `k8s_apt_key` and `k8s_apt_repository` in `cns_values_<version>.yaml`.

Example:
```

## Kubernetes apt resources
k8s_apt_key: "https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg"
k8s_apt_repository: "deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main"
k8s_registry: "registry.aliyuncs.com/google_containers"
```

#### Enable Feature Gates to Cloud Native Stack

`NOTE:` Below config only works with CNS version 16.0 and above which is kubernetes 1.33 and above. 

Update the `templates/kubeadm-init-config.template` with feature gates like below and trigger the installation

```
apiVersion: kubeadm.k8s.io/v1beta4
kind: InitConfiguration
nodeRegistration:
  criSocket: "{{ cri_socket }}"
localAPIEndpoint:
  advertiseAddress: "{{ network.stdout_lines[0] }}"
---
apiVersion: kubeadm.k8s.io/v1beta4
kind: ClusterConfiguration
apiServer:
  extraArgs:
  - name: "feature-gates"
    value: "DynamicResourceAllocation=true"
  - name: "runtime-config"
    value: "resource.k8s.io/v1beta1=true"
  - name: "runtime-config"
    value: "resource.k8s.io/v1beta2=true"
controllerManager:
  extraArgs:
  - name: "feature-gates"
    value: "DynamicResourceAllocation=true"
scheduler:
  extraArgs:
  - name: "feature-gates"
    value: "DynamicResourceAllocation=true"
networking:
  podSubnet: "{{ subnet }}"
kubernetesVersion: "v{{ k8s_version }}"
imageRepository: "{{ k8s_registry }}"
---
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
featureGates:
  DynamicResourceAllocation: true
```
Run the below commands to enable DRA FeatureGates on existing Kubernetes Cluster with Kubernetes v1.33 or newer. 

```sh
sudo sed -i 's/- kube-apiserver/- kube-apiserver\n    - --feature-gates=DynamicResourceAllocation=true\n    - --runtime-config=resource.k8s.io\/v1beta1=true\n    - --runtime-config=resource.k8s.io\/v1beta2=true/' /etc/kubernetes/manifests/kube-apiserver.yaml

sudo sed -i 's/- kube-scheduler/- kube-scheduler\n    - --feature-gates=DynamicResourceAllocation=true/' /etc/kubernetes/manifests/kube-scheduler.yaml
         
sudo sed -i 's/- kube-controller-manager/- kube-controller-manager\n    - --feature-gates=DynamicResourceAllocation=true/' /etc/kubernetes/manifests/kube-controller-manager.yaml
         
sudo sed -i '$a\'$'\n''featureGates:\n  DynamicResourceAllocation: true' /var/lib/kubelet/config.yaml 
         
sudo systemctl daemon-reload; sudo systemctl restart kubelet
```

Run the below command to verify if the features is enabled 

```
kubectl get --raw /metrics  | grep kubernetes_feature_enabled  | grep -i DynamicResourceAllocation
```

If you're planning to enable DRA, then it's recommended to enable CDI with GPU Operator. Set the flag as per below

Example:
```
$ nano cns_values_16.1.yaml

cns_version: 16.1

enable_cdi: yes
```

### Enable MicroK8s 

If you want to use microk8s you can enable the configuration in `cns_values_xx.yaml` and trigger the installation

Example:
```
$ nano cns_values_16.1.yaml

cns_version: 16.1

microk8s: yes
```

### Monitoring on CNS

Deploy Prometheus/Grafan and Elastic Logging stack on Cloud Native Stack

You need to enable `monitoring` in the `cns_values_xx.yaml` like below
```
# Monitoring Stack Prometheus/Grafana with GPU Metrics and Elastic Logging stack
monitoring: no
```
Once stack is install access the Grafana with url `http://<node-ip>:32222` with credentials as `admin/cns-stack`

Once stack is install access the Kibana with url `http://<node-ip>:32221` with credentials as `elastic/cns-stack`

### Storage on CNS

Deploy Storage Provisoner and NFS Provisioner on Cloud Native Stack. 
- It will deply [Local Path Provisoner](https://github.com/rancher/local-path-provisioner?tab=readme-ov-file#local-path-provisioner)
- It will deploy [NFS Provisioner](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner)

You need to enable `storage` in the `cns_values_xx.yaml` like below
```
# Local Path Provisioner and NFS Provisoner as Storage option
storage: no
```

### Load Balancer on CNS

Deploy Load Balancer using NodeIP on Cloud Native Stack, it will deploy [MetalLB](https://metallb.universe.tf/installation/#installation-by-manifest)

You need to enable `loadbalancer` option in the `cns_values_xx.yaml` like below 
```
# Install MetalLB
loadbalancer: no
# Example input loadbalancer_ip: "10.117.20.50/32", it could be node/host IP
loadbalancer_ip: ""
```

###  Confidential Computing on CNS stack

CNS deploys NVIDIA Confidential Containers on top of vanilla Kubernetes (microk8s is not supported). The installation follows the upstream NVIDIA CC reference architecture (kata-deploy + GPU Operator with `sandboxWorkloads.mode=kata`) and works on both **Intel TDX** and **AMD SEV-SNP** hosts. Vendor is auto-detected.

References:
- [Supported platforms](https://docs.nvidia.com/datacenter/cloud-native/confidential-containers/latest/supported-platforms.html)
- [Deployment guide](https://docs.nvidia.com/datacenter/cloud-native/confidential-containers/latest/confidential-containers-deploy.html)

#### Prerequisites (one-time, manual)

1. **CPU + firmware:** Intel Emerald/Granite Rapids (TDX) or AMD Genoa/Milan (SEV-SNP)
2. **OS / kernel:** Ubuntu 25.10 with kernel 6.17+ (or newer with TDX/SNP host support)
3. **GPU:** H100 / H200 / B200 / RTX Pro 6000 BSE (all GPUs on the host must be the same generation)
4. **BIOS** (must be set manually; the playbook cannot configure BIOS without BMC creds):
   - **Intel TDX:** Enable TDX, TME-MT Key Split ≥ 1, SEAM Loader, SGX, x2APIC; Disable Node Interleaving
   - **AMD SEV-SNP:** Enable SEV-SNP, SEV-ES, IOMMU, ACS
5. **GRUB** (typically already set on AMD; the playbook handles Intel automatically):
   - Intel: appends `nohibernate` (TDX cannot survive ACPI S3)
   - AMD: ensure `amd_iommu=on` is in `GRUB_CMDLINE_LINUX_DEFAULT`

> Optional — if you have BMC access and want to set BIOS via Redfish on AMD, populate `bmc_ip` / `bmc_username` / `bmc_password` in `cns_values_<version>.yaml`. Empty values mean "skip BIOS playbook".

#### Install

Edit `cns_values_<version>.yaml` (or rely on `cns_version.yaml` to pick the right one) and ensure `confidential_computing: yes`. Then:

```bash
bash setup.sh install cc
```

What this does:
- Auto-detects CPU vendor (Intel → TDX path, AMD → SNP path)
- Intel: ensures `nohibernate` in GRUB and `kvm_intel tdx=1` module option, then reboots once for TDX to initialize
- AMD: probes `kvm_amd.sev_snp` — on modern kernels, skips the legacy AMDSEV custom-kernel build
- Installs k8s, containerd, helm
- Installs Kata via `helm install kata-deploy oci://ghcr.io/kata-containers/kata-deploy-charts/kata-deploy --version 3.32.0`
- Installs GPU Operator: `--set driver.version=580.178.04 --set sandboxWorkloads.enabled=true --set sandboxWorkloads.mode=kata --set nfd.enabled=true --set nfd.nodefeaturerules=true --version=v26.7.1`
- Configures kubelet `runtimeRequestTimeout: 1200s` and feature gates `KubeletPodResourcesGet=true`, `RuntimeClassInImageCriApi=true`
- Detects Hopper HGX nodes with 8 H100/H200 SXM GPUs and at least 4 NVSwitches, then sets
  `nvidia.com/cc.mode=ppcie` before GPU Operator installation for multi-GPU passthrough
- Labels the node `nvidia.com/gpu.workload.config=vm-passthrough` and the vendor-specific NFD label (`amd.feature.node.kubernetes.io/snp=true` or `intel.feature.node.kubernetes.io/tdx=true`) so kata runtime classes schedule

> If Intel TDX triggers a reboot mid-install, just re-run `bash setup.sh install cc` after the host comes back. Tasks are idempotent; the second run picks up where the first left off.

#### Verify

```bash
# Cluster up?
kubectl get nodes -L nvidia.com/cc.mode.state,nvidia.com/cc.ready.state
# Expect: cc.mode.state=on, cc.ready.state=true

# Runtime classes registered?
kubectl get runtimeclass | grep kata-qemu-nvidia-gpu
# Expect both kata-qemu-nvidia-gpu-tdx and kata-qemu-nvidia-gpu-snp

# Workload test (vendor-aware) — change runtimeClassName based on host:
#   Intel TDX  -> kata-qemu-nvidia-gpu-tdx
#   AMD  SNP   -> kata-qemu-nvidia-gpu-snp
cat <<'EOF' | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: cuda-vectoradd-kata
spec:
  runtimeClassName: kata-qemu-nvidia-gpu-snp   # or kata-qemu-nvidia-gpu-tdx
  restartPolicy: Never
  containers:
    - name: cuda-vectoradd
      image: nvcr.io/nvidia/k8s/cuda-sample:vectoradd-cuda12.5.0-ubuntu22.04
      resources:
        limits:
          nvidia.com/pgpu: "1"
          memory: 16Gi
EOF

kubectl wait --for=jsonpath='{.status.phase}'=Succeeded pod/cuda-vectoradd-kata --timeout=300s
kubectl logs cuda-vectoradd-kata    # expect: "Test PASSED"
kubectl delete pod cuda-vectoradd-kata
```

`NOTE:`
  - microk8s is **not** supported with CC (Step 7 NVIDIA doc).
  - All GPUs on the host must be configured for CC and assigned to one CC VM (multi-vendor mixes will fail at the cc-manager step).
  - Uninstall (`bash setup.sh uninstall`) reverses Intel TDX changes (`nohibernate` + `tdx.conf`) automatically. AMD SNP host kernel state is left untouched (it's a kernel feature, not something the playbook turned on).

### Validation

Run the below command to check if the installed versions are match with predefined versions of the NVIDIA Cloud Native Stack. Here' "Ignored" tasks refer to failed and "Changed/Ok" tasks refer to success.

Run the validation playbook after 5 minutes once completing the NVIDIA Cloud Native Stack Installation. Depends on your internet speed, you need to wait more time.

```
bash setup.sh validate
```
### Upgrade 

Cloud Native Stack can be support life cycle management with upgrade option. you can upgrade the current running stack version to next available version. 

Upgrade option is available from one minor version to next minor version of CNS.

Example: Cloud Native Stack 13.0 can upgrade to 13.1 but 13.x can not upgrade to 14.x

`NOTE:` Currently there's a containerd limitation for upgrade from CNS 14.0 to CNS 16.1, please find the details [here](https://github.com/containerd/containerd/issues/11535)

### Uninstall

Run the below command to uninstall the NVIDIA Cloud Native Stack. Tasks being "ignored" refers to no kubernetes cluster being available.

```
bash setup.sh uninstall
```

`NOTE`
A list of older NVIDIA Cloud Native Stack versions (formerly known as Cloud Native Core) can be found [here](https://github.com/NVIDIA/cloud-native-stack/blob/26.6.0/playbooks/older_versions/readme.md)

<h2> Ansible Playbook Descriptions </h2>

- [Install NVIDIA Cloud Native Stack](#Install-NVIDIA-Cloud-Native-Stack)
- [Validate NVIDIA Cloud Native Stack](#Validate-NVIDIA-Cloud-Native-Stack)
- [Upgrade NVIDIA Cloud Native Stack](#Upgrade-NVIDIA-Cloud-Native-Stack)
- [Uninstall NVIDIA Cloud Native Stack](#Uninstall-NVIDIA-Cloud-Native-Stack)

### Install NVIDIA Cloud Native Stack 

The Ansible NVIDIA Cloud Native Stack installation playbook will do the following:

- Validate if Kubernetes is already installed
- Setup the Kubernetes repository
- Install Kubernetes components 
  - Option to provide the specific kubernetes version
- Install required packages for Docker and Kubernetes
- Setup the Docker Repository
- Install the Docker engine 
  - Option to provide the specific docker version
- Enable and restart Docker and Kubelet
- Disable the Swap for Kubernetes installation
- Initialize the Kubernetes cluster 
  - Option to provide pod network CIDR range
- Copy kubeconfig to home to run kubectl commands
- Install the required networking plugin based on CIDR range
- Taint the control plane node to run all pods on single node
- Check if Helm installed
- Install Helm, if not already installed
- Install the NVIDIA GPU Operator
- Install the NVIDIA Network Operator 

### Validate NVIDIA Cloud Native Stack 

The Ansible NVIDIA Cloud Native Stack validation playbook will do the following:

- Validate if Kubernetes cluster is up
- Check if node is up and running
- Check if all pods are in running state
- Validate that Helm installed
- Validate the GPU Operator pods state
- Report Operating System, Docker, Kubernetes, Helm, GPU Operator versions
- Validate nvidia-smi and cuda liberaries on kubernetes

### Upgrade NVIDIA Cloud Native Stack

The Ansible NVIDIA Cloud Native Stack upgrade playbook will do the following:

- Validate the Cloud Native stack is running
- Update the Cloud Native Stack Version 
- Upgrade the Container runtime and kubernetes components
- Upgrade the Kubernetes cluster to new version
- Upgrade the networking plugin to new version
- Upgrade the GPU Operator to next available version

### Uninstall NVIDIA Cloud Native Stack 

The Ansible NVIDIA Cloud Native Stack uninstall playbook will do the following:

- Reset the Kubernetes cluster
- Remove the Helm package
- Uninstall the Docker and Kubernetes Packages

### Getting Help

Please [open an issue on the GitHub project](https://github.com/NVIDIA/cloud-native-stack/issues) for any questions. Your feedback is appreciated.
