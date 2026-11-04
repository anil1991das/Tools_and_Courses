---
title: "Kubernetes Basics: Pods, Deployments, Services, and ConfigMaps"
description: "Understand the core Kubernetes objects used to run containerized applications, expose services, configure workloads, and inspect cluster state."
slug: "kubernetes-basics-guide"
publishedAt: "2026-11-04"
updatedAt: "2026-11-04"
author: "Editorial Team"
category: "DevOps"
tags:
  - "Kubernetes"
  - "Containers"
  - "Orchestration"
  - "DevOps"
relatedTools: []
relatedArticles:
  - "docker-containers-explained"
  - "docker-compose-guide"
draft: false
---

# Kubernetes Basics: Pods, Deployments, Services, and ConfigMaps

Running a container is straightforward, but operating many containers across multiple machines requires scheduling, health management, networking, and controlled updates.

Kubernetes is an open-source system for orchestrating containerized workloads. It provides APIs and controllers that help maintain the desired state of applications in a cluster.

## What is a Kubernetes cluster?

A Kubernetes cluster contains a control plane and worker nodes.

The control plane exposes the Kubernetes API and manages cluster state. Worker nodes run application workloads.

Important control-plane components include the API server, scheduler, controller manager, and cluster state storage. The exact architecture depends on the distribution and managed service.

## What is a Pod?

A Pod is the smallest deployable unit in Kubernetes. It contains one or more containers that share networking and can share configured storage volumes.

Containers in the same Pod are placed and scheduled together. They can communicate through localhost because they share the Pod network namespace.

A Pod is not normally treated as a permanent server. Controllers create replacement Pods when required.

## What is a Deployment?

A Deployment manages a set of replicated Pods through ReplicaSets.

It describes a desired state, such as the number of replicas and the container image to run. Kubernetes controllers work to reconcile the actual cluster state with that desired state.

A simple Deployment:

~~~yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx:1.27
          ports:
            - containerPort: 80
~~~

The example creates a desired replica count of three. Production deployments should use supported image versions, resource requests and limits, health probes, and suitable security settings.

## What is a Service?

Pods can be replaced and receive different IP addresses. A Service provides a stable network endpoint for a group of Pods selected by labels.

Common Service types include:

- ClusterIP: internal cluster access.
- NodePort: exposes a port on each node.
- LoadBalancer: requests an external load balancer where supported by the environment.
- ExternalName: maps a Service name to an external DNS name.

A Service does not itself run application containers. It provides a stable way to reach selected endpoints.

## ConfigMaps and Secrets

ConfigMaps hold non-confidential configuration data that can be supplied to Pods as environment variables or mounted files.

Secrets are intended for sensitive values such as credentials, but their security depends on cluster configuration, access control, encryption at rest, and operational practices.

Do not assume that a Kubernetes Secret is automatically encrypted simply because it is encoded in YAML. Restrict access and follow the platform's secret-management guidance.

## Apply a manifest

Save a Kubernetes manifest as deployment.yaml and apply it:

~~~bash
kubectl apply -f deployment.yaml
~~~

Inspect Deployments:

~~~bash
kubectl get deployments
~~~

List Pods:

~~~bash
kubectl get pods
~~~

Inspect a resource:

~~~bash
kubectl describe pod POD_NAME
~~~

Read container logs:

~~~bash
kubectl logs POD_NAME
~~~

The kubectl command communicates with the Kubernetes API using the current kubeconfig context. Always check which cluster and namespace are selected before making changes.

## Namespaces

Namespaces organize resources within a cluster. They can separate teams, applications, environments, or administrative boundaries.

Namespaces are not a complete security boundary by themselves. Use Role-Based Access Control (RBAC), network policies, quotas, and admission controls as appropriate.

## Health probes

Kubernetes supports probes to assess container health:

- Startup probe: allows an application time to start.
- Liveness probe: checks whether a container should be restarted.
- Readiness probe: checks whether a container should receive traffic.

A liveness check should not fail simply because a temporary downstream service is unavailable. Poorly designed probes can cause restart loops or remove healthy workloads from service.

## Scaling a Deployment

A Deployment can be scaled:

~~~bash
kubectl scale deployment/web --replicas=5
~~~

The number of replicas is part of the desired state. If autoscaling is configured, the replica count may be adjusted according to defined metrics and policies.

Scaling Pods does not automatically guarantee higher throughput. The application and its dependencies must support the additional load.

## Rolling updates and rollback

Deployments support controlled updates to the Pod template. Kubernetes can gradually replace old Pods with new ones according to the rollout strategy.

Inspect rollout status:

~~~bash
kubectl rollout status deployment/web
~~~

View rollout history:

~~~bash
kubectl rollout history deployment/web
~~~

A rollback may be possible when a previous revision is available:

~~~bash
kubectl rollout undo deployment/web
~~~

Database schema changes and external side effects may not be automatically reversible. Plan application and data compatibility across releases.

## Common Kubernetes mistakes

### Treating Pods as permanent

Pods can be rescheduled or replaced. Store durable data using appropriate persistent storage.

### Using the wrong context

A kubectl command may modify a different cluster than expected. Verify the current context and namespace before destructive operations.

### Forgetting resource requests and limits

Without suitable resource configuration, workloads can compete unpredictably for cluster resources.

### Using liveness probes for dependency checks

A dependency outage should not necessarily cause the application container to restart repeatedly.

### Exposing a Service unintentionally

Check Service type, Ingress or Gateway configuration, firewall policy, and cloud load balancer settings.

## Frequently asked questions

### Is Kubernetes the same as Docker?

No. Docker provides tools to build and run containers. Kubernetes orchestrates containerized workloads across a cluster. Kubernetes can use different container runtimes.

### Does Kubernetes require multiple nodes?

A cluster can run locally on a single node for learning and testing. Production clusters commonly use multiple nodes for capacity and availability.

### What is the difference between a Pod and a Deployment?

A Pod runs containers. A Deployment manages replicated Pods and provides rollout and replacement behavior.

## Further reading

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Kubernetes Concepts: Workloads](https://kubernetes.io/docs/concepts/workloads/)
- [kubectl command reference](https://kubernetes.io/docs/reference/kubectl/)

---
