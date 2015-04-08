# Kubernetes Demo

## Setup

```
fleetctl list-machines
```

## Create the nodes

```
kubectl get nodes
```

```
kubectl create -f nodes/node0.json
kubectl create -f nodes/node1.json
kubectl create -f nodes/node2.json
```

```
kubectl get nodes
```

## Create the postgres pod

```
kubectl create -f pods/postgres.json
```

```
kubectl get pods
```

```
kubectl get pods postgres
```

```
kubectl -o yaml get pods postgres
```

### Create the postgres service

```
kubectl create -f services/postgres.json
```

```
kubectl get services
```
```
kubectl get services postgres
```
```
kubectl -o yaml get services postgres
```

```
psql -h POSTGRES_PORTAL_IP -U postgres
```

## Create the pgview replication controller

```
kubectl create -f replicationcontrollers/pgview-stable-v1.json
```

```
kubectl get replicationControllers
```

```
kubectl get pods
```

### Create the pgview service

```
kubectl create -f services/pgview.json
```

```
curl http://PGVIEW_PORTAL_IP -d @rpc/version.json
```

```
curl http://PGVIEW_PORTAL_IP -d @rpc/sqlfeatures.json
```

Run it again, request should be served from memcache

```
curl http://PGVIEW_PORTAL_IP -d @rpc/sqlfeatures.json
```

### Terminal 2

```
while true; do curl http://PGVIEW_PORTAL_IP -d @rpc/version.json; sleep 1; done
```

## Scaling pgview 

```
kubectl resize --replicas=3 replicationcontrollers pgview-stable-v1
```

```
kubectl get pods
```

## Upgrade pgview using the canary pattern

### Deploy the canary pod

```
kubectl create -f replicationcontrollers/pgview-canary.json
```

```
kubecfg get pods
```

```
kubectl get pods -l "name=pgview,environment=production"
```

### Rolling upgrade

```
kubectl rollingupdate --update-period=5s pgview-stable-v1 -f replicationcontrollers/pgview-stable-v2.json
```

```
kubectl get pods
```
