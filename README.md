# NVIDIA Cloud Native Stack 


## Introduction

NVIDIA Cloud Native Stack (CNS) is a collection of software to run cloud native workloads on NVIDIA GPUs. NVIDIA Cloud Native Stack is based on Ubuntu/RHEL, Kubernetes, Helm and the NVIDIA GPU and Network Operator.

Interested in deploying NVIDIA Cloud Native Stack? This repository has [install guides](./install-guides/readme.md) for manual installations and [ansible playbooks](./playbooks/readme.md) for automated installations.

Interested in a pre-provisioned NVIDIA Cloud Native Stack environment? [NVIDIA LaunchPad](https://www.nvidia.com/en-us/data-center/launchpad/) provides pre-provisioned environments so that you can quickly get started.

## Objective

- CNS comes as a reference architecture that list all components that have been tested successfully together. the CNS reference architecture can be used as specification for production deployments.

- CNS also comes as installation guides and playbook that can be used to instantiate a quick K8s environment with NVIDIA operators. The CNS installation guides and playbook are intended only for test and PoC environments.

  Note: The K8s layer that CNS install guide or playbook deploys is basic (no HA for instance) and as such cannot be used for production. However all NVIDIA components in CNS are fully operable in production environment.

## Life Cycle

When NVIDIA Cloud Native Stack batch is released, the previous batch enters maintenance support and only receives patch release updates. All prior batches enter end-of-life (EOL) and are no longer supported and do not receive patch updates.

> Note: Upgrades are only supported from previous batch to latest batch.


|  Batch  | Status              |
| :-----: | :--------------:|
| [26.6.0](https://github.com/NVIDIA/cloud-native-stack/releases/tag/v26.6.0)                   | Generally Available |
| [25.12.1](https://github.com/NVIDIA/cloud-native-stack/releases/tag/v25.12.1)                   | Maintenance |
| [25.12.0](https://github.com/NVIDIA/cloud-native-stack/releases/tag/v25.12.0)                   | EOL |

`NOTE:` CNS 18.0 and above support Ubuntu 24.04 and Ubuntu 26.04. See the component matrix for deployment restrictions.

For upstream release information, see [Cloud Native Stack Releases](https://github.com/NVIDIA/cloud-native-stack/releases)

## Component Matrix

#### Cloud Native Stack Batch 26.6.0 (Release Date: 1 July 2026)

| CNS Version               | 19.1    | 18.0    | 17.1    |
| :-----:                   | :-----: | :-----: | :-----: |
| Kubernetes                | 1.36.5  | 1.35.6  | 1.34.9  |
| Platforms                 | <ul><li>NVIDIA Certified Server (x86 & arm64)</li></ul> | <ul><li>NVIDIA Certified Server (x86 & arm64)</li></ul> | <ul><li>NVIDIA Certified Server (x86 & arm64)</li></ul> |
| Supported OS              | <ul><li>Ubuntu 24.04 LTS</li><li>Ubuntu 26.04 LTS</li></ul> | <ul><li>Ubuntu 24.04 LTS</li><li>Ubuntu 26.04 LTS</li></ul> | <ul><li>Ubuntu 24.04 LTS</li><li>Ubuntu 26.04 LTS</li></ul> |
| Containerd                | 2.4.1   | 2.4.1   | 2.4.1   |
| NVIDIA Container Toolkit  | 1.20.1  | 1.20.1  | 1.20.1  |
| CRI-O                     | 1.36.1  | 1.35.4  | 1.34.9  |
| CNI (Calico)              | 3.32.0  | 3.32.0  | 3.32.0  |
| NVIDIA GPU Operator       | 26.7.1  | 26.7.1  | 26.7.1  |
| NVIDIA Network Operator   | 26.7.0  | 26.7.0  | 26.7.0  |
| NVIDIA Data Center Driver | 580.178.04 | 580.178.04 | 580.178.04 |
| Helm                      | 4.3.0   | 4.3.0   | 4.3.0   |

Helm defaults to `4.3.0` in all playbook values files. To select another release, set `helm_version` in the values file for your CNS version. See the [Helm 4.3.0 release notes](https://github.com/helm/helm/releases/tag/v4.3.0).

NVIDIA Container Toolkit defaults to `1.20.1` in all playbook values files. To select another release, set `nvidia_container_toolkit_version` in the values file for your CNS version. See the [Container Toolkit 1.20.1 release notes](https://github.com/NVIDIA/nvidia-container-toolkit/releases/tag/v1.20.1).

NVIDIA Network Operator defaults to `26.7.0` in all playbook values files. To select another release, set `network_operator_version` in the values file for your CNS version. See the [Network Operator 26.7.0 release notes](https://docs.nvidia.com/networking/display/kubernetes2670/release-notes.html).

NVIDIA Data Center Driver defaults to `580.178.04` in all playbook values files. To select another release, set `gpu_driver_version` in the values file for your CNS version. See the [NVIDIA Data Center Driver archive](https://developer.nvidia.com/datacenter-driver-archive).

containerd defaults to `2.4.1` in all playbook values files. To select another release, set `containerd_version` in the values file for your CNS version. See the [containerd 2.4.1 release notes](https://github.com/containerd/containerd/releases/tag/v2.4.1).

GPU Operator defaults to `26.7.1` in all playbook values files. To select another release, set `gpu_operator_version` in the values file for your CNS version. See the [GPU Operator 26.7 release notes](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/26.7/release-notes.html) and [platform support matrix](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/26.7/platform-support.html) for component versions and supported configurations.

> NOTE: CNS 18.0 and 19.1 target Ubuntu 24.04 and Ubuntu 26.04. CNS 17.1 targets Ubuntu 24.04.
> NOTE: NVIDIA Network Operator 26.7.0 supports Kubernetes 1.32 through 1.36, covering the CNS versions in this matrix. See the [Network Operator platform support matrix](https://docs.nvidia.com/networking/display/kubernetes2670/platform-support.html) for hardware and operating system requirements.

> Note: Previous upstream Cloud Native Stack release information can be found [here](https://github.com/NVIDIA/cloud-native-stack/tree/25.7.2?tab=readme-ov-file#nvidia-cloud-native-stack-component-matrix)

`main` is the default branch of this repository. Use the `26.10.0` branch for the configuration documented here.

## Software

- Kubernetes
  - [GPU Operator](https://github.com/NVIDIA/gpu-operator)
  - [Network Operator](https://github.com/Mellanox/network-operator)  
  - [FeatureGates](./playbooks/readme.md#enable-feature-gates-to-cloud-native-stack)
- [MicroK8s on CNS](./playbooks/readme.md#enable-microk8s)
- [Storage on CNS](./playbooks/readme.md#storage-on-cns)
- [Monitoring on CNS](./playbooks/readme.md#monitoring-on-cns)
- [Load balancer on CNS](./playbooks/readme.md#load-balancer-on-cns)
- [Ingress Controller and Knative Serving configuration](./playbooks/cns_values_19.1.yaml)

| CNS Version               | 19.1    | 18.0    | 17.1    |
| :-----:                   | :-----: | :-----: | :-----: |
| MicroK8s                  | 1.36    | 1.35    | 1.34    |
| Ingress Controller        | 4.15.1  | 4.15.1  | 4.15.1  |
| LoadBalancer              | MetalLB: 0.16.1 | MetalLB: 0.16.1 | MetalLB: 0.16.1 |
| Storage                   | NFS: 4.0.18 <br /> Local Path: 0.0.36 | NFS: 4.0.18 <br /> Local Path: 0.0.36 | NFS: 4.0.18 <br /> Local Path: 0.0.36 |
| Monitoring                | Prometheus: 86.2.3 <br /> Prometheus Adapter: 5.3.0 <br /> Grafana Operator: 5.24.0 <br /> Elastic: 9.4.2 | Prometheus: 86.2.3 <br /> Prometheus Adapter: 5.3.0 <br /> Grafana Operator: 5.24.0 <br /> Elastic: 9.4.2 | Prometheus: 86.2.3 <br /> Prometheus Adapter: 5.3.0 <br /> Grafana Operator: 5.24.0 <br /> Elastic: 9.4.2 |

# Getting Started

#### Prerequisites

Please make sure to meet the following prerequisites to Install the Cloud Native Stack

- system has direct internet access
- system should have an Operating system Ubuntu 22.04, 24.04, or 26.04
- system has adequate internet bandWidth
- DNS server is working fine on the System
- system can access Google repo(for k8s installation)
- system has only 1 network interface configured with internet access. The IP is static and doesn't change
- UEFI secure boot is disabled
- Root file system should has at least 40GB capacity
- system has 2CPU and 4GB Memory
- At least one NVIDIA GPU attached to the system

#### Installation 

Run the below commands to clone the NVIDIA Cloud Native Stack.

```
git clone -b 26.10.0 https://github.com/francisguillier/cloud-native-stack.git
cd cloud-native-stack/playbooks
```

Update the hosts file in playbooks directory with master and worker nodes(if you have) IP's with username and password like below

```
nano hosts

[master]
<master-IP> ansible_ssh_user=nvidia ansible_ssh_pass=nvidipass ansible_sudo_pass=nvidiapass ansible_ssh_common_args='-o StrictHostKeyChecking=no'
[nodes]
<worker-IP> ansible_ssh_user=nvidia ansible_ssh_pass=nvidiapass ansible_sudo_pass=nvidiapass ansible_ssh_common_args='-o StrictHostKeyChecking=no'
```

Select the CNS version in `cns_version.yaml`, then customize its `cns_values_<version>.yaml` file. Enable the add-ons you need, such as storage, monitoring, MetalLB, the ingress controller, or Knative Serving, using the options available in that file.

Install NVIDIA Cloud Native Stack with:

```
bash setup.sh install
```
For details on selecting a version and customizing values, see [Installation](./playbooks/readme.md#installation)

# Topologies

- Cloud Native Stack allows to deploy:
    - 1 node with both control plane and worker functionalities
    - 1 control plane node and any number of worker nodes

`NOTE:` (Cloud Native Stack does not allow the deployment of several control plane nodes)

# Advanced Settings for NVIDIA Platforms

- [Optimizations for NVIDIA GB200 NVL72](./optimizations/GB200-NVL72.md)
 

# Troubleshooting

[Troubleshoot CNS installation issues](./troubleshooting/README.md)

# Getting help or Providing feedback

Please open an [issue](https://github.com/francisguillier/cloud-native-stack/issues) on the GitHub project for any questions. Your feedback is appreciated.

# Useful Links
- [NVIDIA LaunchPad](https://www.nvidia.com/en-us/data-center/launchpad/)
- [NVIDIA LaunchPad Labs](https://docs.nvidia.com/launchpad/index.html)
- [Cloud Native Stack on LaunchPad](https://docs.nvidia.com/LaunchPad/developer-labs/overview.html)
- [NVIDIA GPU Operator](https://docs.nvidia.com/datacenter/cloud-native/gpu-operator/overview.html)
- [NVIDIA Network Operator](https://docs.nvidia.com/networking/software/cloud-orchestration/index.html)
- [NVIDIA Certified Systems](https://www.nvidia.com/en-us/data-center/products/certified-systems/)
- [NVIDIA GPU Cloud (NGC)](https://catalog.ngc.nvidia.com/)
