---
marp: true
theme: default
paginate: true
class: invert
header: 'Advanced Kubernetes Debugging
footer: 'Konrad Heimel 23-06-08'
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 1rem;
  }
  div.colwrap {
    background-color: inherit;
    color: inherit;
    width: 100%;
    height: 100%;
  }
  div.colwrap div h1:first-child, div.colwrap div h2:first-child {
    margin-top: 0px !important;
  }
  div.colwrap div.left, div.colwrap div.right {
    position: absolute;
    top: 0;
    bottom: 0;
    padding: 70px 35px 70px 70px;
  }
  div.colwrap div.left {
    right: 50%;
    left: 0;
  }
  div.colwrap div.right {
    left: 50%;
    right: 0;
  }
---

# Advanced Kubernetes Debugging

1. Using mirrord to develop inside of kubernetes

---

Agenda

---

# Using mirrord to develop inside of kubernetes

Common Anti-Patterns:

- Replicating kubernetes deployment locally using docker-compose
- Exposing development endpoints to the internet

# About mirrod


# Kubernetes and Linux Namespaces
<!-- _class: default -->

<div class="columns">
<div>

- Linux namespaces provide isolated environments with unique resources
- The container runtime leveraged by Kubernetes creates a new set of namespaces for each pod, ensuring isolated network, IPC, UTS, and PID environments.
- Sidecar containers, deployed in the same pod as the primary container, share most namespaces, enabling inter-container communication.

</div>

<div>

![test](images%2Fpod_sidecar_namespaces.svg)

</div>
</div>

---

# Introduction to Ephemeral Containers

- Ephemeral containers are short-lived, not defined in the pod's specification, and do not share in the pod's lifecycle.
- They come to life as needed, sharing namespaces with an existing container in the same pod.
- They provide powerful tools for understanding and diagnosing the behavior of applications.
- Stable since Kubernetes v1.25

---
#  Using Ephemeral Containers for Debugging

- An ephemeral container can be used to inspect a running pod with issues, without interrupting the pod's operation or requiring specific tools not included in your production image.
- Ephemeral containers can communicate over localhost, use IPC, inspect or signal processes in the pod, and access shared volumes.
- Debug by attaching to a running pod:
  ```bash
  k debug --image=nicolaka/netshoot -it -- /bin/bash
  ```
---

# Installing Inspector Gadget


Install Inspector Gadget using [Krew](https://krew.sigs.k8s.io/) kubectl plugin manager.

```bash
$ kubectl krew install gadget
```

Install Inspector Gadget on kubernetes:
```bash
$ kubectl gadget deploy
Creating Namespace/gadget...
Creating ServiceAccount/gadget...
Creating Role/gadget-role...
Creating RoleBinding/gadget-role-binding...
Creating ClusterRole/gadget-cluster-role...
Creating ClusterRoleBinding/gadget-cluster-role-binding...
Creating DaemonSet/gadget...
Creating CustomResourceDefinition/traces.gadget.kinvolk.io...
Waiting for gadget pod(s) to be ready...
1/1 gadget pod(s) ready
Retrieving Gadget Catalog...
Inspektor Gadget successfully deployed

```

---

---