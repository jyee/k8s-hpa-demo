# Kubernetes HPA Demo

This is a basic demo of using the [Horizontal Pod Autoscaling (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) feature of Kubernetes.

## Demo

### 1. Install the Datadog Agent

The Datadog Agent is installed as a DaemonSet. DaemonSets are like pods, but they have the special feature that Kubernetes will run one DaemonSet per node. This means we can guarantee that for every node, there will be a Datadog Agent running.

Give a brief run through of the Datadog Agent YAML.

Copy `datadog-agent.yaml` to `my-datadog-agent.yaml` and add your API key to the new file. Then apply it.

`kubectl apply -f my-datadog.yaml`

### 2. Install the Datadog Cluster Agent

The Datadog Cluster Agent acts as a proxy to relay information from the Kubernetes API to the Datadog Agents.

In a standard Datadog Agent Daemonset, the Datadog Agent will query the Kubernetes API. While this is easy to implement and understand, at very large scales, it puts a lot of stress on the Kubernetes API. The Datadog Cluster Agent helps alleviate this by performing the Kubernetes API queries and proxying to the individual Datadog Agents.

The Datadog Cluster Agent also services as a custom metrics server. This will allow us in a following step to use metrics from Datadog to control the HPA.

Give a brief run through of the Datadog Cluster Agent YAML.

Copy `datadog-cluster-agent.yaml` to `my-datadog-agent.yaml` and add your API and APP keys to the new file. Then apply it.

`kubectl apply -f my-datadog-cluster-agent.yaml`

### 3. Show off your demo app

Now that monitoring is enabled, you should deploy your application and give an overview of it. If you need an example app, give [Dat-a-dog](https://github.com/jyee/dat-a-dog) a try. It's an extremely simple app that's fun to play with and also can kill a bit of time while metrics start flowing into Datadog.

After you show off the demo app, jump over to Datadog to briefly show that your monitoring is working.

### 4. Create the Autoscaler

### 5. Generate traffic

### 6. Monitor it and watch your pods scale



## Background

Kubernetes version 1.2 added the ability to autoscale the number of pods in a replicaset using a limited set of metrics from within the cluster (e.g. CPU utilized).

Kubernetes version 1.6 extended this to allow the use of any custom metric from within the cluster, including using multiple metrics.

Kubernetes version 1.10 further expanded this feature, adding the ability to use external metrics providers.

Kubernetes version 1.12 added a cooldown or delay (known as downscale stabilization).

