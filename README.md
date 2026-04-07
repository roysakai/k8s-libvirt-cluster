# Kubernetes Homelab Cluster — 6-Node via Vagrant + KVM/Libvirt

A fully automated provisioning setup for a 6-node Kubernetes cluster running on **KVM/Libvirt** (OpenSUSE host), using **Vagrant** for VM lifecycle management. Built as a practical, production-aligned alternative to the VirtualBox/Ubuntu stacks common in most DevOps courses.

---

## Architecture

```
Host: OpenSUSE Tumbleweed — KVM/Libvirt
│
├── control-plane-01   (Ubuntu 22.04 — 2 vCPU / 2GB RAM)
│
├── worker-01          (Ubuntu 22.04 — 2 vCPU / 2GB RAM)
├── worker-02          (Ubuntu 22.04 — 2 vCPU / 2GB RAM)
├── worker-03          (Ubuntu 22.04 — 2 vCPU / 2GB RAM)
├── worker-04          (Ubuntu 22.04 — 2 vCPU / 2GB RAM)
└── worker-05          (Ubuntu 22.04 — 2 vCPU / 2GB RAM)
```

All nodes are provisioned via a single `Vagrantfile` using the `vagrant-libvirt` provider.

---

## Stack

| Component | Version | Role |
|---|---|---|
| Vagrant | 2.x | VM provisioning |
| KVM / Libvirt | — | Hypervisor |
| Ubuntu Server | 22.04 LTS | Node OS |
| kubeadm | 1.29.x | Cluster bootstrapping |
| Flannel | latest | Pod network (CNI) |
| kubectl | 1.29.x | Cluster management |

---

## Prerequisites

- OpenSUSE Tumbleweed (or any Linux distro with KVM support)
- `vagrant`, `vagrant-libvirt` plugin installed
- `libvirt`, `qemu-kvm` installed and running
- At least 16GB RAM and 6 vCPUs available on the host

```bash
# Install vagrant-libvirt plugin
vagrant plugin install vagrant-libvirt
```

---

## Usage

```bash
# Clone the repository
git clone https://github.com/YOUR_USERNAME/k8s-libvirt-cluster
cd k8s-libvirt-cluster

# Bring up all 6 VMs and provision the cluster
vagrant up

# SSH into the control plane
vagrant ssh control-plane-01

# Verify cluster health
kubectl get nodes
```

Expected output after provisioning:

```
NAME               STATUS   ROLES           AGE   VERSION
control-plane-01   Ready    control-plane   5m    v1.29.x
worker-01          Ready    <none>          4m    v1.29.x
worker-02          Ready    <none>          4m    v1.29.x
worker-03          Ready    <none>          4m    v1.29.x
worker-04          Ready    <none>          4m    v1.29.x
worker-05          Ready    <none>          4m    v1.29.x
```

---

## What's Provisioned Automatically

- Container runtime (containerd)
- kubeadm init on control plane
- Worker nodes joined via kubeadm token
- Flannel CNI deployed
- `kubectl` configured for the vagrant user
- kubeconfig exported for access from the host

---

## Test Workload

A sample Nginx deployment is included to validate the cluster:

```bash
kubectl apply -f manifests/nginx-test.yaml
kubectl get pods -o wide
```

---

## Why KVM/Libvirt Instead of VirtualBox

VirtualBox is the default in most DevOps tutorials, but KVM/Libvirt is what you'll find in real data centers and cloud provider hypervisor stacks. Running this lab on KVM provides:

- Better performance (hardware virtualization, no ring-3 overhead)
- Closer parity with production environments
- Native Linux integration — no proprietary kernel modules

---

## Project Context

This cluster was built as part of a hands-on DevOps learning path, adapting all lab exercises from a structured course to a KVM-based environment rather than following the prescribed VirtualBox/AWS path. The adaptation required solving real networking, driver compatibility, and provisioner configuration challenges that don't appear in guided tutorials.

---

## License

MIT
