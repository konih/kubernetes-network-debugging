# The Challenge with Containers and Networking

While containers ensure that the runtime environment is consistent and reproducible, they do not always do the same for networking.
Differences in networking configurations and behaviors remain one of the primary causes of issues in containerized environments.

Understanding and debugging these differences is crucial for a smooth and efficient deployment on Kubernetes.

## Kubernetes Networking Explained

- Kubeproxy, iptables, and IPVS, 

---

# Inspector Gadget

- Collection of eBPF-based tools for analysing Kubernetes applications.
- Collects kernel-level information like system calls or network activity and enriches it with Kubernetes metadata.
- Mechanism for deploying eBPF programs to Kubernetes clusters.


# eBPF


- eBPF is a Linux kernel technology that allows running programs triggered by events in the kernel inside the user space.
- programs are written in a restricted subset of C and compiled to a special bytecode.
- the bpf bytecode is loaded into the kernel and validated before execution.
- programs are validated to ensure they always terminate and cannot access memory outside of the program's memory space.
- eBPF programs must have finite complexity, so the verifier can evaluate all execution paths.

---

# Some Gadget Tools

- trace-loop
- top [block-io,ebpf,file,tcp]
- 
---

# Installing Inspector Gadget

The inpector-gadget cli can be installed as a krew kubectl plugin:

```bash
kubectl krew install gadget
```

To be able to deploy eBPF programs to your cluster, you need to install the gadget-server component:

```bash
kubectl gadget deploy
```
This installs `gadget` as a daemonset on your cluster. A pod on each node is needed to deploy eBPF programs.

---

# Using Inspector Gadget


k gadget advise network-policy monitor -n microservices-demo -o activity.out
k gadget advise network-policy monitor -n microservices-demo --output activity.out

---