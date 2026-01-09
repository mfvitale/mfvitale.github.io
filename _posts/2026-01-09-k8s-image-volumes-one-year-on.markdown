---
layout: post
title:  "K8s Image Volumes: One Year On"
date:  2026-01-09 11:00:00 +0100
categories: blog
---

Last time I blogged was a year ago, and before that it had been two years, so progress!

In the [previous post](https://www.mfvitale.me/blog/2025/02/10/k8s-image-volume-support.html) about the topic,
I tried to test it locally on a minikube cluster but without success because the feature was in a very early development stage and not fully supported across the different runtimes.

## One year later: where ImageVolume stands

Over the last year, the ImageVolume feature made steady progress, moving from Alpha in Kubernetes 1.31 to Beta and becoming enabled by default in 1.35.
Initially, the feature required manual enablement and only supported mounting entire OCI images, with full support limited to CRI-O and partial support in containerd.
[Kubernetes 1.33](https://kubernetes.io/blog/2025/04/29/kubernetes-v1-33-image-volume-beta/) promoted ImageVolume to Beta, adding `subPath` and `subPathExpr` support along with dedicated kubelet metrics, but the feature remained disabled by default due to incomplete containerd support.
With [Kubernetes 1.35](https://kubernetes.io/blog/2025/12/17/kubernetes-v1-35-release/#volumesource-oci-artifact-and-or-image), full beta support landed in both CRI-O and containerd, allowing the feature to be enabled by default and making ImageVolume work out of the box on standard Kubernetes installations.

## Trying it with minikube

Unfortunately, the latest minikube release (1.37) is still based on:

* Kubernetes v1.34.0
* containerd v1.7.23
* CRI-O v1.29.1

The good news is that the **main branch** already moved to:

* Kubernetes v1.35.0
* containerd v2.2.1
* CRI-O v1.35.0

The bad news is that this is still work in progress.

So I decided to build the minikube ISO with the latest changes to start a cluster using the containerd runtime:

```shell
git clone https://github.com/kubernetes/minikube.git
cd minikube
make buildroot-image
GO_VERSION=1.22.10 make out/minikube-amd64.iso
```

Then I started a new cluster with the custom image:

```shell
minikube start -p k8s-1.35 --iso-url=file://$(pwd)/out/minikube-amd64.iso --kubernetes-version=v1.35.0
```

> **NOTE:** The `--kubernetes-version` flag is required because we are using the v1.37.0 minikube binary, which has `DefaultKubernetesVersion = "v1.34.0"` built in.

I checked the containerd version to make sure the ISO was used:

```shell
minikube ssh -p k8s-1.35 "containerd --version"
```

…and it was containerd 1.7.27, not 2.2.1! After some digging, I realized minikube was using the Docker driver, which uses a container-based approach (kicbase) instead of the provided ISO.

## Using the right driver

I deleted the cluster:

```shell
minikube delete -p k8s-1.35
```

and restarted with the `qemu` driver:

```shell
minikube start -p k8s-1.35 \
    --driver=qemu \
    --iso-url=file://$(pwd)/out/minikube-amd64.iso \
    --kubernetes-version=v1.35.0
```

After starting with a VM driver, I verified the right versions:

```shell
minikube ssh -p k8s-1.35 "containerd --version"  # Should show 2.2.1
minikube ssh -p k8s-1.35 "crio --version"        # Should show 1.35.0
```

Then I created a test pod with an Image Volume mounted:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: image-volume
spec:
  containers:
  - name: shell
    command: ["sleep", "infinity"]
    image: debian
    volumeMounts:
    - name: volume
      mountPath: /volume
  volumes:
  - name: volume
    image:
      reference: quay.io/crio/artifact:v2
      pullPolicy: IfNotPresent
```

but it failed with the following error:

```text
Warning  Failed  3s (x9 over 111s)  kubelet Error: Error response from daemon: invalid mount config for type "bind": field Source must not be empty
```

At first glance this error made no sense, but it turned out to be a strong hint that a wrong container runtime was used.

## Driver vs container runtime

In Minikube, these two concepts live at different layers of the stack.

### Driver

The driver defines how the Kubernetes node itself is created and run.  
This is the infrastructure layer: VM vs container, and which technology is used.

Common drivers include Docker, Podman, VirtualBox, KVM, Hyperkit, and VMware.

### Container runtime

The container runtime runs *inside* that node and is responsible for executing Kubernetes pods.  
Typical options are Docker (deprecated), containerd, and CRI-O.  

For example:

```shell
minikube start --driver=docker --container-runtime=containerd
```

Docker is used to create the Kubernetes node, while containerd runs the application containers inside it.  
In short: the **driver** controls how the node runs; the **container runtime** controls how containers run inside Kubernetes.

## Using the right container runtime

I deleted the cluster again:

```shell
minikube delete -p k8s-1.35
```

and recreated it properly with containerd:

```shell
minikube start -p k8s-1.35 \
    --driver=qemu \
    --container-runtime=containerd \
    --iso-url=file://$(pwd)/out/minikube-amd64.iso \
    --kubernetes-version=v1.35.0
```

This time it worked! I was able to shell into the pod and list files mounted from the volume:

```shell
kubectl exec image-volume -it -- bash -c "ls /volume/ && cat /volume/dir/file"
```

That's all!  
I hope this write-up is useful to someone else, and, at the very least, to my future self the next time I forget how Minikube actually works.

