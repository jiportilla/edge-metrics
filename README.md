# Edge Monitoring
Cognitive Edge Monitoring with Prometheus Operator.

Overall workflow:

1. Install JSON Exporter Edge Service
2. Install & Configure a k8s compatible cluster
3. Deploy a Prometheus operator and configure Custom Resource Definition
4. Install a Grafana dashboard for Edge monitoring


![Prometheus architecture ](docs/prometheus-design.png)

1. Install JSON Exporter Edge service

Folllow the steps provided at:

[https://github.com/jiportilla/edge_json_exporter](https://github.com/jiportilla/edge_json_exporter)

2. Install & configure k3s as follows:

## Install and configure a k3s edge cluster

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

3. Install a Prometheus operator instance with:

4. Configure CRDs in the Prometheus operator with:

5. Create an Edge monitoring dashboard in Grafana with:


