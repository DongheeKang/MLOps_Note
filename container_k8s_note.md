# Network/IT Preparation


## Contents

* [Docker](#docker)
* [Kubernetes](#kubernetes)

<br/><a name="docker"></a>
## Docker container

* docker configuration for a single-replica Prometheus service
```
$ docker service create --replicas 1 --name my-prometheus \
    --mount type=bind,source=/tmp/prometheus.yml,destination=/etc/prometheus/prometheus.yml \
    --publish published=9090,target=9090,protocol=tcp \
    prom/prometheus
```


<br/><a name="kubernetes"></a>
## Kubernetes

* Deployment
```
$ kubectl create -f ./load-balancer-example.yaml
$ kubectl get deployments
$ kubectl rollout status deployment/nginx-deployment
$ kubectl describe deployments
$ kubectl get pods --show-labels
```


<div><br/>
&raquo; Back to <a href="#contents">Contents</a> | <a href="../docs/README.md">Docs</a>
</div>
