---
marp: true
theme: default
paginate: true
class:
  - default
markdown.marp.enableHtml: true
inlineSVG: true
style: |
  .columns {  
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 1rem;
  }
---

![bg opacity:.5](images/img.png)

# Advanced Kubernetes Debugging Techniques

<!-- footer: Konrad F. Heimel, 2023-08-17 -->

---

# Agenda

![bg right:25%](images/bg_vert.png)

- **mirrord** 💻<br>
  Transfer your IDE into the Kubernetes cluster
- **inspector gadget** 🔍<br>
  Cloud-native debugging using eBPF
- **ephemeral containers** 🌩️<br>
  Debug running pods without tools in the container image

---

![bg right:25%](images/bg_vert3.png)

# What is **mirrord**? 🧩

- 🚀 Connects a local process to your Kubernetes cluster.
- 📲 Comes with CLI & plugins for IntelliJ and VS Code.
- 🌩️ Debug in the cloud, without deploying.
- 🔄 Test locally in cloud conditions:
  - 🚫 Without local deployment
  - 🚫 Without CI/CD
  - 🚫 Without deploying untested code

---

# How does it work?

1. Creates a **mirrord-agent** in the cluster:
  - 🔄 Clones/steals & forwards traffic
2. Overrides local process' syscalls to:
  - 🔊 Listen to agent's incoming traffic.
  - 🔜 Send out traffic from remote pod.
  - 📂 Access remote file system.
  - 🌍 Merge pod's environment with local.

---

# Language/Framework Support

- 📚 Hooks `libc`, supporting:
  - Rust
  - Node
  - Python
  - Java
  - Kotlin
  - Ruby
  - ... and others!
- 🚀 Also supports Go, not using `libc`.

---

<!-- _class: invert -->

# Installation on Cluster?

<div class="columns">

<div class="left">
<font size="5">

- 🚫 Nothing persistent.
- 🌌 Short-lived pod/container for proxy.
- 🔌 Only needs `kubectl` configured.
- ⛔ **Incompatible with Pod Security Standards.**

</font>
</div>

<div class="right">
<font size="3">

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mirrord-agent-lgfcl4ujer-mxbgp
spec:
  containers:
    - image: ghcr.io/metalbear-co/mirrord:3.56.1
      name: mirrord-agent
      securityContext:
        capabilities:
          add:
            - SYS_ADMIN
            - SYS_PTRACE
            - NET_RAW
            - NET_ADMIN
        runAsGroup: 7318
      volumeMounts:
        - mountPath: /host/run
          name: hostrun
        - mountPath: /host/var
          name: hostvar
  hostPID: true
  volumes:
    - hostPath:
        path: /run
      name: hostrun
    - hostPath:
        path: /var
      name: hostvar
```
</font>

</div>

</div>

---

# Configuration 🔧

<div class="columns">
<div class="left">
<font size="5">

-  `target`: Pod/group you connect to.
- `env`: Merge pod's and local's environment.
- `networking.mode`:
  - `steal`: Capture incoming
  - `mirror`: Sniff & forward a copy
-  `fs.mode`:
  - `localwithoverrides`: Read Kubelet-generated files.

</font>
</div>
<div class="right">

```json
{
  "kube_context": "my-cluster",
  "accept_invalid_certificates": false,
  "target": {
    "path": "deploy/spring-demo-chart",
    "namespace": "mirrord-demo"
  },
  "feature": {
    "network": {
      "incoming": {
        "mode": "mirror",
        "outgoing": true
      },
      "dns": true
    },
    "fs": {
      "mode": "localwithoverrides"
    },
    "env": true
  },

  "telemetry": false
}
```

</div>
</div>

---

# **Advantages of mirrord**

- 🔄 Mirrors traffic ensuring safety.
- 📂 Flexibly manage traffic and file operations.
- 🌟 Superior to local clusters: Handles complex environments.
- 🚫 No installation of infrastructure on cluster required.
- 🚫 No cluster deployments: Stable code remains.
- 🌐 Connects specific services to the cloud.

![bg left:25%](images/bg_vert.png)

---

<!-- _class: default -->

# **mirrord vs. Telepresence** 🤜🤛

<div class="columns">
<div class="left">


- 🚀 Process-level operation (no daemons).
- 🌌 Run multiple services concurrently.
- 🛠 No cluster installation needed.
- 🔍 Duplicates traffic by default.
- 📌 IDE extensions available!

</div>
<div class="right">
    <div style="display: flex; justify-content: center; align-items: center; flex-direction: column; height: 100%;">
        <img src="images/inspector_gadget_logo.png" alt="Inspector Gadget Logo" style="margin-bottom: 20px;">
        <img src="images/telepresence_logo.png" alt="Telepresence Logo">
    </div>
</div>

</div>

---

Certainly! Here's a slide to announce a live demo:

---

<!-- _class: default -->

![bg center:50% opacity:.8](images/demo_bg.png)



<div style="text-align: center; margin-top: 40px;">

<font size="20">
🎥 Live Demo
</font>

</div>

