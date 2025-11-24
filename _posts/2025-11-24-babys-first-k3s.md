---
layout: newpost
title: "Babys first k3s cluster"
date: 2025-11-24
categories: [tech]
tags: [k3s, raspberry]
---

In my line of work, I've been in contact with k8s many time; defending, incident reponse, exercises etc.
I think is it well overdue to setup a small envionment of my own to do some real life testing! :dizzy:

[_k3s_](https://k3s.io/):
- adpted for IOT, i.e. low resource devices
- packaged as a single 70MB binary

---

# Pre-req (activate cgroups)

> Cgroups, or control groups, are a Linux kernel feature that allows the management of resource allocation (like CPU and memory) for a collection of processes

Add `cgroup_enable=cpuset cgroup_memory=1 cgroup_enable=memory` to `/boot/firmware/cmdline.txt` as this enables:
- `cgroup_enable=cpuset`: Enables the cpuset controller - process isolation to certain cores
- `cgroup_memory`:  active memory controller in cgroups - track and limit memory in pods
- `cgroup_enable=memory`: Legacy - similar as the one above

# Installation

Install kubectl:
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

I also like to use `k` (`alias k=kubectl`) and `echo "source <(kubectl completion zsh)" >> ~/.zshrc`

# Access from other host

Copy `/etc/rancher/k3s/k3s.yaml` to our remote host `~/.kube/config`. If needed, open firewall `sudo ufw allow from 192.168.0.0/24 to any port 6443 proto tcp`. Also remeber to change the server IP in the config from 127.0.0.1.

# A first look

```sh
$ sudo k get pods -A
NAMESPACE     NAME                                      READY   STATUS      RESTARTS   AGE
kube-system   coredns-6d668d687-2zdlt                   1/1     Running     0          9m53s
kube-system   helm-install-traefik-8lttg                0/1     Completed   1          9m54s
kube-system   helm-install-traefik-crd-bk2dw            0/1     Completed   0          9m54s
kube-system   local-path-provisioner-869c44bfbd-p8fxg   1/1     Running     0          9m53s
kube-system   metrics-server-7bfffcd44-bxdzk            1/1     Running     0          9m53s
kube-system   svclb-traefik-c4768877-clz4s              2/2     Running     0          9m14s
kube-system   traefik-865bd56545-rc4lg                  1/1     Running     0          9m15s
```

- `coredns`: DNS; translates `api.default.svc.cluster.local` and more
- `helm-install`: one-time jobs to install traefik
- `local-path-provisioner`: Automatic storage backend
- `metrics-server`: collects stats like CPU for `k top` to work
- `svclb-traefik`: makes it possible to reach the cluster from outside
- `traefik`: Ingress controller, handles all HTTP/HTTPS traffic


# Create a pod

Create namespace
```sh
k create namespace first
```

Create a pod with configMap, service, deplyment and networking

<details markdown="1">
<summary>Expand</summary>

```sh
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: first
  name: nginx-config
data:
  index.html: |
    <html>
      <head>
        <title>My first k3s page</title>
      </head>
      <body>
        <h1>Welcome</h1>
        <p>This page is served from a Kubernetes ConfigMap</p>
      </body>
    </html>
---
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: first
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - name: config-volume
          mountPath: /usr/share/nginx/html
      volumes:
      - name: config-volume
        configMap:
          name: nginx-config
---
apiVersion: v1
kind: Service
metadata:
  namespace: first
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ClusterIP
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: first
  name: nginx-ingress
spec:
  rules:
  - host: r5.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: nginx-service
            port:
              number: 80

```
</details>


Check status:
```sh
$k get -f nginx-deploy.yaml
NAME                     DATA   AGE
configmap/nginx-config   1      5m30s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   1/1     1            1           5m30s

NAME                    TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
service/nginx-service   ClusterIP   10.43.35.63   <none>        80/TCP    5m30s

NAME                                      CLASS     HOSTS      ADDRESS         PORTS   AGE
ingress.networking.k8s.io/nginx-ingress   traefik   r5.local   192.168.0.187   80      26s
``` 

```sh
$ k top pod -A
NAMESPACE     NAME                                      CPU(cores)   MEMORY(bytes)
first         nginx-deployment-6d7b844d58-x7r7q         0m           6Mi
kube-system   coredns-6d668d687-2zdlt                   2m           14Mi
kube-system   local-path-provisioner-869c44bfbd-p8fxg   1m           8Mi
kube-system   metrics-server-7bfffcd44-bxdzk            8m           22Mi
kube-system   svclb-traefik-c4768877-clz4s              0m           0Mi
kube-system   traefik-865bd56545-rc4lg                  1m           20Mi
```

```sh
❯ curl r5.local -I
HTTP/1.1 200 OK
```

# Conclusion

This was really easy and fun to get up and running, but this is where the real work begins!
- Network policies and logs
- DNS monitoring
- Syscalls, file read etc.
- Monitoring of the cluster
- etc.

And lots more! :clap: