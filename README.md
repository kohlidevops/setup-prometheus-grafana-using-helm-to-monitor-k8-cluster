# To setup Prometheus and Grafana using Helm package to monitor Kubernetes master and worker nodes

## step-1: To setup Kubernetes cluster on AWS Cloud using KOPS (Kubernetes operation)

```
https://github.com/kohlidevops/kops-to-setup-kubernetes/blob/main/README.md
```

### Install helm on master node

SSH to master machine and install helm3 package

```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/853a6cd3-3166-4431-b9ff-5190846f4023)

Next, We have to add the helm stable charts

```
helm repo add stable https://charts.helm.sh/stable
```

To add prometheus helm repo

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
```

To create a prometheus namespace

```
kubectl create namespace prometheus
```

### Install kube-prometheus-stack

The helm repo prometheus-operator or kube-prometheus-stack comes with grafana by default

```
helm install stable prometheus-community/kube-prometheus-stack -n prometheus
```

kube-prometheus-stack has been installed without fail

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/169ef9bf-20c0-42f7-a646-ee17accfaaad)


### To check the prometheus and grafana pods

```
kubectl get pods -n prometheus
```

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/9a69c464-8d20-48d5-bd80-93baef5a8b3b)

```
kubectl get svc -n prometheus
```

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/340e1a3f-ee95-4df6-a5c6-86c64b5b3817)

### To use a Loadbalancer for prometheus and grafana

In order to make prometheus and grafana available outside the cluster, use LoadBalancer or NodePort instead of ClusterIP

To edit the prometheus service

```
kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus

edit -> type: LoadBalancer
save and exit
```

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/8dc26410-6697-4a02-9c21-bb33a27641b5)

To edit the grafana service

```
kubectl edit svc stable-grafana -n prometheus

edit -> type: LoadBalancer
save and exit
```

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/fd4ff0da-ea40-4dde-9487-42a5324e7b19)

```
kubectl get svc -n prometheus
```

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/f235b705-1c9c-4bd2-9292-f93baf918305)

#### To find the secrets for grafana

By default username - admin

For password, first you find the namespace

```
kubectl get secret --namespace prometheus
```

Now you can fetch the password for grafana

```
kubectl get secret --namespace prometheus stable-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/55f5ac59-99d4-4fa4-895b-78301d7dd1fa)

### To create a kubernetes monitoring dashboard in Grafana

Navigate to Grafana dashboard and click on "+" symbol -> Import dashboard

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/291417e0-b309-45e5-b75e-4b0bfe8624e8)

dashboard id - 12740 and Load it.

Select "Prometheus" as the endpoint under prometheus data sources drop down.

Click "Import"

This will show monitoring dashboard for all cluster nodes

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/af3dff91-fa5f-42c9-a64a-824943a4ec29)

### How to Create Kubernetes Cluster Monitoring Dashboard?

Click "+" button on left panel and select "Import"

Enter 3119 dashboard id under Grafana.com Dashboard.

Click "Load"

Select "Prometheus" as the endpoint under prometheus data sources drop down.

Click "Import"

This will show monitoring dashboard for all cluster nodes

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/7ea9ac74-1391-468d-8a7c-6dc0eaa22433)

### Create POD Monitoring Dashboard

For creating a dashboard to monitor the cluster

Click "+" button on left panel and select "Import"

Enter 6417 dashboard id under Grafana.com Dashboard

Click "Load"

Select "Prometheus" as the endpoint under prometheus data sources drop down

Click "Import"

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/4253e94d-31bd-4240-82b0-f44d6fa09c7d)

![image](https://github.com/kohlidevops/setup-prometheus-grafana-using-helm-to-monitor-k8-cluster/assets/100069489/7f624b9f-91e1-456e-aea7-892dd1e6f2c2)

Now, I can able to monitor all the dashboards from Grafana with the help of prometheus!
