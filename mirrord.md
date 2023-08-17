---
marp: true
theme: default
---

# **mirrord**
Debugging in Kubernetes

---

# What is **mirrord**?

- It connects a local process to your Kubernetes cluster.
- Comes with a CLI and plugins for IntelliJ and VS Code.
- Debug your code in the cloud, without deploying it.
- Lets you test locally in cloud conditions:
    - Without local deployment
    - Without CI/CD
    - Without deploying untested code

---

# How does it work?

1. Creates a mirrord-agent in the cluster, that:
  - clones or steals traffic and forwards it to the local process
  - forwards traffic from the local process to the cluster
2. Override the local process' syscalls to:
   - Listen to incoming traffic from the agent, instead of local sockets.
   - Intercept outgoing traffic and send it out from the remote pod, instead of locally.
   - Read and write files to the remote file system.
   - Merge the pod's environment variables with the local process' environment variables.

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

<div class="columns">
<div class="left">

- Does **NOT** install anything persistently.
- Spawns a short-living pod/container for the proxy.
- Works with Kubernetes API. Only needs `kubectl` configured.
- Not compatible with any Pod Security Standards
</div>
<div class="right">

```yaml
    apiVersion: v1
    kind: Pod
    spec:
      containers:
       - image: ghcr.io/metalbear-co/mirrord:3.56.1
         securityContext:
           capabilities:
             add:
               - SYS_ADMIN
               - SYS_PTRACE
               - NET_RAW
               - NET_ADMIN
    volumes:
      - hostPath:
          path: /run
          type: ""
        name: hostrun
      - hostPath:
          path: /var
          type: ""
```

</div>

</div>


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
