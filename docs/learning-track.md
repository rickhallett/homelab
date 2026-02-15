# The Curriculum

*8 weeks from theory to practice. No shortcuts. No training wheels.*

---

## The Premise

Each phase builds on the last. You can't understand LFS without understanding what `chroot` does. You can't appreciate what k3s automates without having done it manually. You can't debug a cluster without understanding the Linux it runs on.

The sequence exists so nothing feels like magic. Magic is just ignorance wearing a costume.

---

## The Arc

| Week | Phase | What | Machine |
|------|-------|------|---------|
| 0 | Prep | Download everything. Air-gap kit. | USB drive |
| 1 | Foundation | Arch manual install. No `archinstall`. | X230 |
| 1-2 | Foundation | OverTheWire Bandit wargames | Ryzen (online) |
| 2-3 | Deep Dive | Linux From Scratch (air-gapped) | X1 Carbon |
| 3-4 | Concepts | Kubernetes the Hard Way | Ryzen |
| 4 | Hardware | Rack nodes, install OSes | New cluster |
| 5 | Operations | k3s bootstrap | Cluster |
| 6-7 | Production | Workloads, GitOps, observability | Cluster |
| 8+ | Mastery | Chaos engineering. Break everything. | Cluster |

---

## Phase 0: Air-Gap Survival Kit

*Do this first. Before anything else.*

**See:** [Air-Gap Kit](./air-gap-kit.md)

Download ISOs, Arch Wiki, LFS sources, KTHW guide, k3s docs. Put them on a USB drive. When you're air-gapped and stuck at 2am, Google won't save you. These will.

### Pre-Install (on any Arch partition)

```bash
pacman -S base-devel man-db man-pages vim tmux git arch-wiki-docs
```

---

## Phase 1: Arch Manual Install

**Machine:** X230
**Time:** Weekend 1
**Goal:** Boot into a working Arch system. No GUI. No `archinstall`. Terminal and determination.

### What You Learn

| Concept | Why It Matters |
|---------|----------------|
| UEFI/BIOS boot chain | How machines start |
| Partitioning, filesystems | What "storage" actually means |
| `chroot` | The ancestor of containers |
| `systemd` | How services run |
| `pacman` | Package management |
| Network configuration | Because WiFi doesn't configure itself |

### Setup

Dual boot the X230:
- Partition 1: Omarchy (the escape hatch, install first)
- Partition 2: Raw Arch (the playground)

Expect to reinstall multiple times. That's not failure. That's the curriculum.

### Resources

- Arch Wiki Installation Guide (downloaded)
- `man` pages (installed locally)
- Your patience

### Done When

- [ ] System boots to shell
- [ ] Networking works
- [ ] User account exists
- [ ] SSH server running
- [ ] No desktop environment (you don't need one)

---

## Phase 2: OverTheWire Bandit

**Machine:** Ryzen (online)
**Time:** Week 1-2
**Goal:** CLI fluency. Stop Googling basic commands.

### What You Learn

| Levels | Skills |
|--------|--------|
| 0-5 | SSH, cat, find, file, hidden files |
| 6-12 | Permissions, grep, sort, uniq, tr, base64 |
| 13-20 | SSH keys, netcat, SSL, diff |
| 21-27 | Cron, bash scripting, git internals |
| 28-34 | Shells, escape sequences, race conditions |

### Access

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
# Password: bandit0
```

### Resources

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
- `man` pages for every command you encounter
- "The Linux Command Line" by William Shotts (downloaded)

### Done When

- [ ] Level 25+ completed (ideally 34)
- [ ] Pipes, redirection, find, grep are second nature
- [ ] You've stopped Googling `tar` flags

---

## Phase 3: Linux From Scratch

**Machine:** X1 Carbon
**Time:** Weekend 2-3
**Goal:** Build a complete Linux system from source. Understand what's inside.

This is the crucible. LFS teaches you what a "distribution" actually is: about 80 packages, compiled in a specific order, with specific dependencies. After LFS, containers make sense. Init systems make sense. "Why does glibc matter?" has an answer.

### What You Learn

| Concept | Why It Matters |
|---------|----------------|
| Cross-compilation | How toolchains bootstrap themselves |
| Package dependencies | Why build order matters |
| Library linking | Static vs dynamic, `ldd`, `ldconfig` |
| Kernel configuration | `make menuconfig` isn't magic |
| Init systems | What happens between kernel and shell |
| Build debugging | When `./configure` fails (it will) |

### Setup

Dual boot the X1 Carbon:
- Partition 1: Raw Arch (host system for building)
- Partition 2: Omarchy (escape hatch)
- LFS target: Separate partition created during the process

**Air-gap after downloading sources.** This is the point.

### Build Times (i7-1185G7)

| Step | Time |
|------|------|
| GCC Pass 1 | ~15-20 min |
| GCC Pass 2 | ~20-30 min |
| Glibc | ~10-15 min |
| Linux kernel | ~12-18 min |
| **Full build** | **~10-16 hours** |

Yes, it takes a weekend. Yes, it's worth it.

### Resources

- LFS Book (PDF + HTML, downloaded)
- LFS source tarballs + patches (~500MB, downloaded)
- MD5 checksums (verify everything)

### Done When

- [ ] LFS system boots
- [ ] Shell prompt appears
- [ ] You can explain what glibc does

---

## Phase 4: Kubernetes the Hard Way

**Machine:** Ryzen
**Time:** Week 3-4
**Goal:** Understand what k3s automates before letting it automate.

KTHW isn't a deployment guide. It's an education. You provision certificates manually. You start etcd manually. You configure the API server manually. Then, when k3s does it all for you, you know what "it" is.

### What You Learn

| Component | What It Does |
|-----------|--------------|
| Certificate PKI | How trust is established |
| etcd | Where cluster state lives |
| API server | The brain |
| Scheduler | Pod placement decisions |
| Controller manager | Reconciliation loops |
| kubelet | Node agent |
| CNI | Pod networking |

### Approach

1. Read through KTHW completely
2. Optionally implement on VMs or containers
3. Focus on understanding, not completion
4. Take notes on each component

### Resources

- [Kubernetes the Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)
- kubectl cheat sheet
- k8s docs (downloaded)

### Done When

- [ ] Can explain what each component does
- [ ] Understand certificate chain
- [ ] Know what k3s automates (and why it's useful)

---

## Phase 5: Cluster Bootstrap

**Machine:** All 6 nodes
**Time:** Week 4-5
**Goal:** A real, working, multi-node cluster.

### Hardware Setup (Week 4)

1. [ ] Unbox and label all nodes (cp-1, cp-2, cp-3, wk-1, wk-2, wk-3)
2. [ ] Install RAM upgrades
3. [ ] Mount with VESA brackets or stack on shelf
4. [ ] Connect to switch
5. [ ] Connect power (individual switches for chaos simulation)

### OS Installation

| Node | Hostname | IP | Distro | Why |
|------|----------|-----|--------|-----|
| 1 | cp-1 | 192.168.1.101 | Debian 12 | Stability for etcd |
| 2 | cp-2 | 192.168.1.102 | Debian 12 | Stability for etcd |
| 3 | cp-3 | 192.168.1.103 | Debian 12 | Stability for etcd |
| 4 | wk-1 | 192.168.1.111 | Arch | Rolling release chaos |
| 5 | wk-2 | 192.168.1.112 | Ubuntu 24.04 | "Corporate" environment |
| 6 | wk-3 | 192.168.1.113 | Arch | Rolling release chaos |

Three distros. Heterogeneous on purpose. Because production isn't uniform and neither should your education be.

### Base Checklist (Each Node)

```bash
□ Set hostname
□ Configure static IP
□ Create admin user with sudo
□ Install SSH server
□ Disable password auth
□ Copy SSH public key from cockpit
□ Install base packages (curl, git, vim, tmux)
□ Configure NTP
□ Disable swap
□ Verify: ssh admin@<hostname> works
```

### k3s Bootstrap (Week 5)

**First control plane (cp-1):**
```bash
curl -sfL https://get.k3s.io | sh -s - server \
  --cluster-init \
  --disable traefik
```

**Additional control planes (cp-2, cp-3):**
```bash
curl -sfL https://get.k3s.io | sh -s - server \
  --server https://cp-1:6443 \
  --token <token-from-cp-1>
```

**Workers (wk-1, wk-2, wk-3):**
```bash
curl -sfL https://get.k3s.io | sh -s - agent \
  --server https://cp-1:6443 \
  --token <token-from-cp-1>
```

### Done When

- [ ] `kubectl get nodes` shows all 6
- [ ] Control plane is HA (3 nodes)
- [ ] Test pod deploys and runs
- [ ] Pod can schedule on any worker

---

## Phase 6: Production Patterns

**Time:** Week 6-7
**Goal:** Deploy real workloads. Implement GitOps. Add observability.

### Storage

- [ ] Install Longhorn for distributed storage
- [ ] Create PersistentVolumeClaim
- [ ] Kill a node. Verify data survives.

### Networking

- [ ] Install Cilium CNI
- [ ] Configure NetworkPolicy
- [ ] Test pod isolation

### Observability

- [ ] Prometheus + Grafana
- [ ] Loki for logs
- [ ] Dashboard for cluster health
- [ ] Alerts configured

### GitOps

- [ ] ArgoCD installed
- [ ] App-of-apps pattern
- [ ] Deploy via git commit
- [ ] Auto-sync verified

### Done When

- [ ] Apps deploy when you push to git
- [ ] Metrics visible in dashboards
- [ ] Storage survives node death

---

## Phase 7: Chaos Engineering

**Time:** Week 8+
**Goal:** Break everything. Understand failure modes. Build intuition.

### The Scenarios

| Scenario | Method | What You Learn |
|----------|--------|----------------|
| Worker death | `sudo poweroff` on wk-1 | Pod rescheduling |
| Control plane death | `sudo poweroff` on cp-2 | etcd quorum |
| Network partition | `iptables -A INPUT -s cp-1 -j DROP` | Split brain |
| Resource exhaustion | `stress --vm 4 --vm-bytes 14G` | OOMKiller, eviction |
| Graceful drain | `kubectl drain wk-1 --ignore-daemonsets` | Controlled eviction |
| Namespace deletion | `kubectl delete ns production` | Recovery procedures |
| Certificate issues | Simulate expired certs | PKI debugging |

### For Each Failure

1. What happened?
2. How did you detect it?
3. How did you recover?
4. What did you learn?

Document everything in the learning log. This is interview material.

---

## The Path

```
Week 1-3:   Padawan    — Manual installs, wargames, LFS. Building foundation.
Week 4-6:   Apprentice — Cluster running, basic ops. Learning the tools.
Week 7-10:  Knight     — GitOps, observability, chaos. Building intuition.
Week 11+:   Master     — Teaching others, writing about it. Paying forward.
```

---

## After 8 Weeks

You can honestly say:

> "I built a Linux system from source following LFS. I run a 6-node heterogeneous k8s cluster at home with Debian control plane and mixed Arch/Ubuntu workers. I've done manual Arch installs, completed OverTheWire wargames, and understand Kubernetes from the certificate layer up because I did KTHW before touching k3s. I can work with or without AI assistance."

That's not a claim. That's a credential.

---

*The best time to start was yesterday. The second best time is now.*
