# Edge Monitoring
Cognitive Edge Monitoring with Prometheus Operator.

###Edge Node EventLog Rest API
Anax provides an Event Log rest API,

For example, to list the event logs for the current or all registrations run:

`hzn eventlog list`


Event Log API source code is avaialble at:
[EventLog API](https://github.com/open-horizon/anax/blob/master/cli/eventlog/eventlog.go)

Here is an example of the information provided with `hzn eventlog list -l` command:

```
 {
    "record_id": "10",
    "timestamp": "2020-12-09 12:37:21 -0700 PDT",
    "severity": "error",
    "message": "Error starting containers: API error (500): driver failed programming external connectivity on endpoint 7996f592634d22c5583f74b0404cc77fd69969879fa5ec321e0cf2c70ef38043-acme-motion-detection-service-gpu (b3623ba8977d822fc4b0d47614e6cabb07b4f7d9b7004e5829aaa6440bb248d7): Bind for 0.0.0.0:9080 failed: port is already allocated",
    "event_code": "error_start_container",
    "source_type": "agreement",
    "event_source": {
      "agreement_id": "7996f592634d22c5583f74b0404cc77fd69969879fa5ec321e0cf2c70ef38043",
      "workload_to_run": {
        "url": "acme-motion-detection-service-gpu",
        "org": "ipcluster",
        "version": "1.0.0",
        "arch": "amd64"
      },
      "dependent_services": [],
      "consumer_id": "IBM/ipcluster-agbot",
      "agreement_protocol": "Basic"
    }
  }
```
Notice the `severity` and `message` content in the resulting JSON. This information will exported to Prometheus.


##Overall workflow:

1. Install JSON Exporter Edge Service
2. Install & Configure a k8s compatible cluster
3. Deploy a Prometheus operator and configure Custom Resource Definition
4. Install a Grafana dashboard for Edge monitoring


![Prometheus architecture ](docs/prometheus-design.png)

##1. Install JSON Exporter edge service

The Prometheus development community has created a JSON Exporter to scrape remote JSON data by JSONPath. Source code is available at [https://github.com/prometheus-community/json_exporter](https://github.com/prometheus-community/json_exporter)

Install and configure the JSON Exporter edge service by following the steps provided at:

[https://github.com/jiportilla/edge_json_exporter](https://github.com/jiportilla/edge_json_exporter)

##2. Install & configure a k8s0compatible cluster

For example k3s:

### Install and configure a k3s edge cluster

K3s is a highly available, certified Kubernetes distribution designed for production workloads in unattended, resource-constrained, remote locations or inside IoT appliances.

This section provides a summary of how to install k3s (rancher), a lightweight and small kubernetes cluster, on Ubuntu 18.04. (For more detailed instructions, see the k3s documentation)

1. Either login as root or elevate to root with sudo -i
2. The full hostname of your machine must contain at least 2 dots. Check the full hostname:
	`hostname`
	hostname
	If the full hostname of your machine contains less than 2 dots, change the hostname:
	
	`hostnamectl set-hostname <your-new-hostname-with-2-dots>`

3. Install k3s with:

```
curl -sfL https://get.k3s.io | sh -

# Check for Ready node, 
#  takes maybe 30 seconds
 
k3s kubectl get node
```

Or other compatible k8s offerings.

Next, deploy **CoreOS** Prometheus Operator as follows:

## 3. Deploy a Prometheus operator and configure Custom Resource Definition

The Prometheus ecosystem consists of multiple components, many of which are optional.

### Architecture

This diagram illustrates the architecture of Prometheus and some of its ecosystem components:

![Prometheus architecture ](docs/prometheus-architecture.png)


[architecture](https://prometheus.io/docs/introduction/overview/)

###Prometheus Operator

The Prometheus Operator for Kubernetes provides easy monitoring definitions for Kubernetes services and deployment and management of Prometheus instances.

![Edge Prometheus Operator ](docs/Edge-Prometheus-operator.png)

This repository collects Kubernetes manifests, Grafana dashboards, and Prometheus rules combined with documentation and scripts to provide easy to operate end-to-end Kubernetes cluster monitoring with Prometheus using the Prometheus Operator. The container images support AMD64, ARM64, ARM and PPC64le architectures.

The content of this project is written in jsonnet and is an extension of the fantastic kube-prometheus project.


Components included in this package:

- The Prometheus Operator
- Highly available Prometheus
- Highly available Alertmanager
- Prometheus node-exporter
- kube-state-metrics
- CoreDNS
- Grafana
- SMTP relay to Gmail for Grafana notifications (optional)

Resources available at: 
[https://github.com/carlosedp/cluster-monitoring](https://github.com/carlosedp/cluster-monitoring)

## Quickstart for K3S
To deploy the monitoring stack on your K3s cluster, there are four parameters that need to be configured in the vars.jsonnet file:

1. Set k3s.enabled to true.
2. Change your K3s master node IP(your VM or host IP) on k3s.master_ip parameter.
3. Edit suffixDomain to have your node IP with the .nip.io suffix or your cluster URL. This will be your ingress URL suffix.
4. Set traefikExporter enabled parameter to true to collect Traefik metrics and deploy dashboard.
After changing these values to deploy the stack, run:

```
$ make vendor
$ make
$ make deploy

# Or manually:

$ make vendor
$ make
$ kubectl apply -f manifests/setup/
$ kubectl apply -f manifests/
```

### Ingress
Now you can open the applications:

To list the created ingresses, run kubectl get ingress --all-namespaces, if you added your cluster IP or URL suffix in vars.jsonnet before rebuilding the manifests, the applications will be exposed on:

- Grafana on https://grafana.[your_node_ip].nip.io,
- Prometheus on https://prometheus.[your_node_ip].nip.io
- Alertmanager on https://alertmanager.[your_node_ip].nip.io

### Pre-reqs

The project requires json-bundler and the jsonnet compiler. The Makefile does the heavy-lifting of installing them. You need Go already installed:

```
git clone https://github.com/carlosedp/cluster-monitoring
cd cluster-monitoring
make vendor
# Change the jsonnet files...
make

```
After this, a new customized set of manifests is built into the manifests dir. To apply to your cluster, run:

`make deploy`

To uninstall run:

`make teardown`
