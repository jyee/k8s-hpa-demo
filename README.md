# Kubernetes HPA Demo

This is a basic demo of using the [Horizontal Pod Autoscaling (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) feature of Kubernetes.

## Demo

### 1. Install the Datadog Agent

The Datadog Agent is installed as a DaemonSet. DaemonSets are like pods, but they have the special feature that Kubernetes will run one DaemonSet per node. This means we can guarantee that for every node, there will be a Datadog Agent running.

`kubectl apply -f datadog.yaml`

### 2. Install the Datadog Cluster Agent

### 3. Show off your demo app

### 4. Create the Autoscaler

### 5. Generate traffic

### 6. Monitor it and watch your pods scale



## Background

Kubernetes version 1.2 added the ability to autoscale the number of pods in a replicaset using a limited set of metrics from within the cluster (e.g. CPU utilized).

Kubernetes version 1.6 extended this to allow the use of any custom metric from within the cluster, including using multiple metrics.

Kubernetes version 1.10 further expanded this feature, adding the ability to use external metrics providers.

Kubernetes version 1.12 added a cooldown or delay (known as downscale stabilization).

