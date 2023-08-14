---
marp: true
theme: default
---

# **mirrord**
Debugging in Kubernetes

---

# What is **mirrord**?

- Test your code in the cloud from the beginning.
- No CI, no deployment hassles.
- It connects a local process to your Kubernetes cluster.
- It doesn't just mirror traffic. It does more.
- Lets you test locally in cloud conditions:
    - Without local deployment
    - Without CI/CD
    - Without deploying untested code

---

# How does it work?

1. Hooks to the local process input/output:
    - Network traffic
    - File access
    - Environment variables
2. Proxies them to the Kubernetes cluster.
3. Code “thinks” it’s running in the cloud.

---

# Language/Framework Support

- Hooks `libc`, so it supports:
    - Rust
    - Node
    - Python
    - Java
    - Kotlin
    - Ruby
    - ... and others!
- Also supports Go, which doesn't use `libc`.

---

# Installation on Cluster?

- Does **NOT** install anything persistently.
- Spawns a short-living pod/container for the proxy.
- Works with Kubernetes API. Only needs `kubectl` configured.
- If restricted, allow image: `ghcr.io/metalbear-co/mirrord`.

---

# Traffic Management

- Mirror traffic or use `--steal` flag to intercept.
- Supports service meshes like Istio or Linkerd.
    - Mirroring not supported, but stealing is.

---

# Troubleshooting mirrord

- Statically linked binaries? Need dynamic linking.
    - For Go, add `import "C"`.
- Running on MacOS and protected by SIP? Try:
    - Copy binary to unprotected directory.
    - `sudo codesign --remove-signature ./<your-binary>`

---

# Domain Resolution Issue?

- If in `feature.fs.mode` set to local, change to `localwithoverrides`.
- Ensures correct reading of Kubelet-generated files like `/etc/resolv.conf`.

---

# Why **mirrord** and not a remote debugger?

- No deployment needed to the cluster.
- Less disruptive since the stable code is still running.

---

# Why not a local cluster like minikube?

- Some environments are too complex for local machines.
- **mirrord** connects just the service you're working on to the cloud.
- Can also connect non-containerized processes to local Kubernetes.

---

# Safety with **mirrord**

- Mirrors traffic by default, stable code still runs.
- Control which functionality runs where:
    - Read files and get incoming traffic from cloud.
    - Write files and send outgoing traffic locally.

---

# **mirrord** vs Telepresence

- Works on process level, no daemons.
- Multiple services can run simultaneously.
- Doesn't need cluster installation.
- Duplicates traffic by default.
- IDE extensions for IntelliJ & VS Code.
