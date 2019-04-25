# Kubernetes HPA Demo

This is a basic demo of using the [Horizontal Pod Autoscaling (HPA)](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) feature of Kubernetes.

## Demo

### Preparation

You'll obviously need a demo Kubernetes environment to run this, I prefer using [Minikube](https://github.com/kubernetes/minikube). Minikube is ubiquitous, so there are tons of resources available should you encounter issues. Note that if you use Minikube, you should enable the metrics server by running `minikube addons enable metrics-server`.

You'll also need a [Datadog account](https://app.datadoghq.com).

### 1. Install the Datadog Agent

The Datadog Agent is installed as a DaemonSet. DaemonSets are like pods, but they have the special feature that Kubernetes will run one DaemonSet per node. This means we can guarantee that for every node, there will be a Datadog Agent running.

Give a brief run through of the Datadog Agent YAML. Note that the RBAC is baked into this file.

Copy `datadog-agent.yaml` to `my-datadog-agent.yaml` and add your API key to the new file. Then apply it.

`kubectl apply -f my-datadog.yaml`

### 2. Install the Datadog Cluster Agent

The Datadog Cluster Agent acts as a proxy to relay information from the Kubernetes API to the Datadog Agents.

In a standard Datadog Agent Daemonset, the Datadog Agent will query the Kubernetes API. While this is easy to implement and understand, at very large scales, it puts a lot of stress on the Kubernetes API. The Datadog Cluster Agent helps alleviate this by performing the Kubernetes API queries and proxying to the individual Datadog Agents.

The Datadog Cluster Agent also serves as a custom metrics server. This will allow us in a following step to use metrics from Datadog to control the HPA.

Give a brief run through of the Datadog Cluster Agent YAML. Note that we've also baked the RBAC into this file. Below that we setup the Cluster Agent services (both as Cluster Agent and metrics server). You may also want to talk about the prometheus autodiscovery.

Note that we're pinning the version to 1.2.0. There's a new Cluster Agent coming soon and we want to be safe. This may eventually be moved back to `latest`.

Copy `datadog-cluster-agent.yaml` to `my-datadog-agent.yaml` and add your API and APP keys to the new file. Then apply it.

`kubectl apply -f my-datadog-cluster-agent.yaml`

### 3. Show off your demo app

Now that monitoring is enabled, you should deploy your application and give an overview of it. If you need an example app, give [Dat a dog?](https://github.com/jyee/dat-a-dog) a try. It's an extremely simple app that's fun to play with and also can kill a bit of time while metrics start flowing into Datadog.

If you're using minikube to run *Dat a dog?*, you can launch the app in a brwoser by running:

```
minikube service dat-a-dog
```

After you show off the demo app, jump over to Datadog to briefly show that your monitoring is working.

### 4. Create the Autoscaler

Next we want to create the autoscaler. As with the other YAML files, we've got RBAC at the top to set up permissions. At the bottom, you'll see the Horizontal Pod Autoscaler definition. Here we can set the parameters for scaling, which resource we want to scale and the metrics to look at.

If you're using the *Dat a dog?* app, set the app name to `dat-a-dog`. You can try setting the metric name to `trace.flask.request.hits` and the targetAverageValue to `10`. This is a fantastic opportunity to talk about what metrics to scale on.

### 5. Generate traffic

`watch` is a fantastic command line tool that allows you to constantly refresh a command. You can install it by running `brew install watch`.

In one terminal, set a watch on your kubernetes pods:

```
watch -n 2 kubectl get pods
```

Play with your app to generate some traffic. It will take a while for the HPA to register the metric (it polls every 15 seconds by default). Note that this will not likely generate enough traffic to scale.

Next run a loop:

```
while sleep 0.5; do curl <YOUR APP'S URL>; done
```

You can check the state of the HPA by running a watch on it:

```
watch -n 2 kubectl get hpa biggerizer -o yaml
```

You'll start to see annotations about the current status of the metric.

### 6. Monitor it and watch your pods scale

If you've picked a good metric and a low value, your pods should start autoscaling. You can also jump into Datadog to see those pods register.

## Background

Kubernetes version 1.2 added the ability to autoscale the number of pods in a replicaset using a limited set of metrics from within the cluster (e.g. CPU utilized).

Kubernetes version 1.6 extended this to allow the use of any custom metric from within the cluster, including using multiple metrics.

Kubernetes version 1.10 further expanded this feature, adding the ability to use external metrics providers.

Kubernetes version 1.12 added a cooldown or delay (known as downscale stabilization).

