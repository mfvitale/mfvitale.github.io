---
layout: post
title:  "K8s image volume support"
date:  2025-02-10 11:00:00 +0100
categories: blog
---

Hey it has been a while since my last writing on this blog! Last two years were very intensive both at work and personally, but that is not what I would like to write about today. 

Some time ago, a colleague reported a Kubernetes blog post about [Image Volumes](https://kubernetes.io/docs/tasks/configure-pod-container/image-volumes/), and as I used to do, I put this on my "save for later" list of things. 

During recents weeks, I thouht that this news feature can help me with a feature for [Debezium](https://debezium.io/) and so I staterd to experiment with it. In this blog post I'm going to share my journey in through it. 

First of all, what are `image volume`? It's just a way to use an OCI image/artifact as a volume, nothing more, nothing less. 
The scope is to allow users to package files and share them among containers in a pod without including them in the main image, thereby reducing vulnerabilities and simplifying image creation.
You can think of it as a way to extend your application images with libraries and/or custom code.

This [Kubernetes Enhancement Proposal (KEP)](https://github.com/kubernetes/enhancements/issues/4639) is still in development and it is avaiable in Kubernetes 1.31 as `alpha`. 

That was my knowledge before starting to play with it but then I discovered other interesting things. First of all, I could not find anyone else on the internet who had really tested it, just a few Medium articles mimicking the one from the Kubernetes blog.
So I decided to put my hands on it.

My goal was to put an external library in an image and mount it to a pod that runs a Java app that requires that library.

Usually, I use [minikube](https://minikube.sigs.k8s.io/docs/) to play with k8s on my machine. So I updated it to K8s 1.31 and ran it with 

```bash
minikube start --feature-gates='ImageVolume=true'
```

The feature-gates flag just enables the `ImageVolume` feature as it has not enabled by default. Then I tried the example described above.

Unfortunately, no lucky at first shot. I was getting a general error on the pod

 ```
 message: 'Error response from daemon: invalid volume specification:
```

After a little bit of reseach without any findings, I just contacted Sascha Grunert, one of the guys behind the implementation, and he told me that the current implementation only works with [CRI-O](https://cri-o.io/) runtime.
I have to be honest that I knew nothing about it, so after some research and learning time, I understood that I was needing a way to tell minikube to switch the [runtime](https://minikube.sigs.k8s.io/docs/runtimes/), from the default containerd to the cri-o one. 

So, the command to start minikube became:

```bash
minikube start --feature-gates='ImageVolume=true' --container-runtime=cri-o
```

and this were the starting logs

```bash
😄  minikube v1.35.0 on Fedora 41
🆕  Kubernetes 1.32.0 is now available. If you would like to upgrade, specify: --kubernetes-version=v1.32.0
✨  Using the docker driver based on existing profile
👍  Starting "minikube" primary control-plane node in "minikube" cluster
🚜  Pulling base image v0.0.46 ...
💾  Downloading Kubernetes v1.31.0 preload ...
    > preloaded-images-k8s-v18-v1...:  371.11 MiB / 371.11 MiB  100.00% 4.45 Mi
🔄  Restarting existing docker container for "minikube" ...
❗  Image was not built for the current minikube version. To resolve this you can delete and recreate your minikube cluster using the latest images. Expected minikube version: v1.34.0 -> Actual minikube version: v1.35.0
🎁  Preparing Kubernetes v1.31.0 on CRI-O 1.24.6 ...
🔗  Configuring CNI (Container Networking Interface) ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
    ▪ Using image registry.k8s.io/ingress-nginx/controller:v1.11.3
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.4.4
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.4.4
🔎  Verifying ingress addon...
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

	minikube addons enable metrics-server

🌟  Enabled addons: storage-provisioner, default-storageclass, ingress, dashboard
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

As you can see, there was a trace of `CRI-O`. Good!

Well, my joy ended quickly since the pod was failing with the following error:

```bash
Warning  Failed     0s (x5 over 41s)    kubelet            Error: mount.HostPath is empty
```

After some research and another help by Sascha, I understood that the problem was the wrong CRI-O version - `1.29.1` instead of `1.31.0`. The latter is the one that supports the new image volume. 

So it was clear that the image used by the minikube nodes had an older version of CRI-O runtime. After some reasearch, I was able to find the related [configuration](https://github.com/kubernetes/minikube/blob/86ca9841adfc02f44977a287cd2248f33e689d9c/deploy/iso/minikube-iso/package/crio-bin/crio-bin.mk#L7C20-L7C27)
and it seemed that there was also the possibility to [build you own iso image](https://minikube.sigs.k8s.io/docs/contrib/building/iso/). 

I changed the version in the `crio-bin.mk` file and tried with the build commands, but it ended up with a failure, see the reported [issue](https://github.com/kubernetes/minikube/issues/20382).

In the end, I was not able to effectively test it but I learned a lot of new things. 

First of all, since the KEP targes both OCI image and artifact, I learned the difference between 

[*OCI Image*](https://github.com/opencontainers/image-spec/blob/main/manifest.md#image-manifest):

* Runnable container image with an operating system, application, and dependencies
* Designed to be executed as a container
* Includes an entrypoint or command to start a process
* Typically used for deploying applications

[*OCI Artifact*](https://github.com/opencontainers/image-spec/blob/main/manifest.md#guidelines-for-artifact-usage):

* Non-executable container format
* Stores and distributes any type of content (not just containers)
* Used for storing supplementary data like:
  * Helm charts
  * Software bill of materials (SBOM)
  * Signatures
  * Configuration files
* Cannot be directly run as a container

And finally, despite the OCI artifact are not supported for now, I think this will be the most sensible why to use the image volume. 

The KEP is [scheduled](https://github.com/orgs/kubernetes/projects/200/views/1) to be moved to `beta` with in the k8s 1.33. 
The `containerd` support is in progress through the PR [10570](https://github.com/containerd/containerd/pull/10579). 

Although I was not able to effectively test it, I think this is a good addition to Kubernetes and I will surely give it another try once the support for `containerd` is released or someone helps identify the issue on minikube to test it with CRI-O runtime.

I hope this helped someone else.
