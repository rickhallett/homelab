# The Architecture

*How the pieces fit together. Why they're arranged this way.*

---

## Physical Layout

```
┌─────────────────────────────────────────────────────────────┐
│                        THE COCKPIT                          │
│                                                             │
│  Ryzen 7 6800H · 32GB · Hyprland · tmux · kubectl          │
│  2× ultrawide monitors                                      │
│                                                             │
│  This is the command center. Not a cluster node.           │
│  The captain doesn't sleep in the engine room.             │
└─────────────────────────────┬───────────────────────────────┘
                              │
                              │ 2.5GbE
                              │
┌─────────────────────────────┴───────────────────────────────┐
│              TP-Link TL-SG108-M2 Switch                     │
│              8-port · 2.5GbE · fanless · silent             │
└──┬──────┬──────┬──────┬──────┬──────┬──────┬───────────────┘
   │      │      │      │      │      │      │
   │      │      │      │      │      │      └── spare
   │      │      │      │      │      │
┌──┴──┐┌──┴──┐┌──┴──┐┌──┴──┐┌──┴──┐┌──┴──┐
│cp-1 ││cp-2 ││cp-3 ││wk-1 ││wk-2 ││wk-3 │
│ DEB ││ DEB ││ DEB ││ARCH ││ UBU ││ARCH │
└─────┘└─────┘└─────┘└─────┘└─────┘└─────┘
   └──────┴──────┴──────┴──────┴──────┘
                  │
         ┌────────┴────────┐
         │  Power Strip    │
         │  8-way · surge  │
         │  individual sw. │
         └─────────────────┘

         Individual switches for chaos.
         Kill a node with a flick.
```

---

## Hardware

All nodes are HP ProDesk 400 G4 Mini. Same chassis. Same management. Same spare parts.

| Component | Spec |
|-----------|------|
| CPU | Intel i5-8500T (6C/6T @ 3.5GHz) |
| RAM | 16GB DDR4-2666 |
| Storage | 256GB SATA SSD |
| Network | 1GbE onboard |
| Power | ~35W TDP |
| Noise | Quiet enough for a living room |

These aren't powerful machines. They don't need to be. k8s scheduling, pod networking, etcd quorum — none of it requires an i9. It requires understanding.

---

## Node Assignments

| Hostname | IP | Role | Distro | Purpose |
|----------|-----|------|--------|---------|
| cp-1 | 192.168.1.101 | Control Plane | Debian 12 | etcd, API server, scheduler |
| cp-2 | 192.168.1.102 | Control Plane | Debian 12 | etcd, API server, scheduler |
| cp-3 | 192.168.1.103 | Control Plane | Debian 12 | etcd, API server, scheduler |
| wk-1 | 192.168.1.111 | Worker | Arch | Workloads, rolling update chaos |
| wk-2 | 192.168.1.112 | Worker | Ubuntu 24.04 | Workloads, "corporate" env |
| wk-3 | 192.168.1.113 | Worker | Arch | Workloads, chaos target |

### Why Mixed Distros

- **Debian on control plane:** Stability. etcd doesn't appreciate surprise kernel upgrades.
- **Arch on workers:** Rolling releases break things. Breaking things teaches things.
- **Ubuntu on one worker:** Because that's what most companies run. Touch it. Know it.

Homogeneous clusters are comfortable. Heterogeneous clusters are educational.

---

## Network Configuration

### IP Addressing

| Range | Purpose |
|-------|---------|
| 192.168.1.100-199 | Cluster nodes (static) |
| 192.168.1.200-254 | DHCP pool |
| 10.42.0.0/16 | k3s pod CIDR |
| 10.43.0.0/16 | k3s service CIDR |

### DNS

Either add to `/etc/hosts` on the cockpit:

```
192.168.1.101 cp-1 cp-1.lab.local
192.168.1.102 cp-2 cp-2.lab.local
192.168.1.103 cp-3 cp-3.lab.local
192.168.1.111 wk-1 wk-1.lab.local
192.168.1.112 wk-2 wk-2.lab.local
192.168.1.113 wk-3 wk-3.lab.local
```

Or run local DNS (Pi-hole, dnsmasq). The second option is more work and more learning.

### Firewall Rules

| Port | Protocol | Purpose |
|------|----------|---------|
| 22 | TCP | SSH |
| 6443 | TCP | Kubernetes API |
| 2379-2380 | TCP | etcd |
| 10250 | TCP | kubelet |
| 10251 | TCP | kube-scheduler |
| 10252 | TCP | kube-controller-manager |
| 8472 | UDP | Flannel VXLAN |

---

## Kubernetes Topology

### Control Plane (HA)

```
┌─────────────────────────────────────────────────────────────┐
│                    CONTROL PLANE                            │
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │    cp-1     │  │    cp-2     │  │    cp-3     │         │
│  │             │  │             │  │             │         │
│  │  etcd ──────┼──┼──── etcd ───┼──┼──── etcd   │         │
│  │  (leader)   │  │  (follower) │  │  (follower) │         │
│  │             │  │             │  │             │         │
│  │  api-server │  │  api-server │  │  api-server │         │
│  │  scheduler  │  │  scheduler  │  │  scheduler  │         │
│  │  controller │  │  controller │  │  controller │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
│                                                             │
│              Quorum: 2 of 3 for cluster health              │
│                                                             │
│  Lose 1 node: cluster continues (quorum maintained)         │
│  Lose 2 nodes: cluster down (quorum lost)                   │
└─────────────────────────────────────────────────────────────┘
```

### Workers

```
┌─────────────────────────────────────────────────────────────┐
│                        WORKERS                              │
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │    wk-1     │  │    wk-2     │  │    wk-3     │         │
│  │    Arch     │  │   Ubuntu    │  │    Arch     │         │
│  │             │  │             │  │             │         │
│  │  kubelet    │  │  kubelet    │  │  kubelet    │         │
│  │  containerd │  │  containerd │  │  containerd │         │
│  │  kube-proxy │  │  kube-proxy │  │  kube-proxy │         │
│  │             │  │             │  │             │         │
│  │  [pods]     │  │  [pods]     │  │  [pods]     │         │
│  └─────────────┘  └─────────────┘  └─────────────┘         │
│                                                             │
│  Node Labels:                                               │
│    wk-1: os=arch, chaos=allowed                            │
│    wk-2: os=ubuntu, env=corporate                          │
│    wk-3: os=arch, chaos=allowed                            │
└─────────────────────────────────────────────────────────────┘
```

Labels let you target workloads. Schedule agent pods on Arch nodes. Put production workloads on Ubuntu. Or don't. The point is you can.

---

## Storage

### Option A: Local Path (k3s Default)

Simple. Each node has local storage. No replication. If the node dies, data dies.

Good for learning. Bad for production. Perfect for chaos experiments.

### Option B: Longhorn

Distributed block storage with replication across nodes.

```
┌─────────────────────────────────────────────────────────────┐
│                       LONGHORN                              │
│                                                             │
│  ┌─────────┐      ┌─────────┐      ┌─────────┐             │
│  │  wk-1   │      │  wk-2   │      │  wk-3   │             │
│  │         │      │         │      │         │             │
│  │ replica │◄────►│ replica │◄────►│ replica │             │
│  │   1     │      │   2     │      │   3     │             │
│  └─────────┘      └─────────┘      └─────────┘             │
│                                                             │
│         Replication factor: 3                               │
│         Survives: 2 node failures                           │
│         Learns: distributed storage is hard                 │
└─────────────────────────────────────────────────────────────┘
```

Install Longhorn. Create a PVC. Kill a node. Watch replication happen. Understand why Ceph engineers look tired.

---

## Failure Domains

| Failure | Impact | Recovery |
|---------|--------|----------|
| 1 worker down | Pods reschedule | Automatic |
| 2 workers down | Reduced capacity, some pods Pending | Manual capacity planning |
| 1 CP down | Cluster continues | Automatic (quorum) |
| 2 CP down | **Cluster unavailable** | Restore a CP node |
| Network partition | Split brain possible | Depends on partition |
| Power loss | Everything down | Manual restart, etcd recovery |
| Arch rolling update breaks kubelet | wk-1 or wk-3 down | That's why Debian runs the control plane |

This table isn't comprehensive. Reality will find failures not on this list.

---

## Maintenance Procedures

### Rolling OS Updates (Arch Workers)

```bash
# Cordon the node (no new pods scheduled)
kubectl cordon wk-1

# Drain workloads (graceful eviction)
kubectl drain wk-1 --ignore-daemonsets --delete-emptydir-data

# SSH and update
ssh wk-1 "sudo pacman -Syu && sudo reboot"

# Watch for node to come back
kubectl get nodes -w

# Uncordon (allow scheduling again)
kubectl uncordon wk-1
```

Repeat for each Arch worker. Never update them all at once.

### etcd Backup

```bash
# On any control plane node
sudo k3s etcd-snapshot save --name backup-$(date +%Y%m%d)

# List snapshots
sudo k3s etcd-snapshot ls
```

Backups you don't test are backups that don't work. Restore one. Make sure it works. Then trust it.

### Certificate Rotation

k3s handles this automatically. But you did KTHW, so you understand *what* it's handling. That understanding is the point.

---

*A cluster you don't maintain is a cluster that will teach you about catastrophic failure.*

*A cluster you maintain carefully is a cluster that will teach you about graceful failure.*

*Either way, you learn.*
