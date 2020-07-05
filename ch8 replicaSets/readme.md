# ReplicaSets

ReplicaSet acts as a cluster-wide Pod manager, ensuring that the right types and numbers of Pods are running at all times. 

- self-healing
- uniform Pods

ReplicaSet is designed to represent a single microservice inside the architecture. 

## Reconciliation loops 

The notion is: desired state vs. observed or current state.

The loop is constantly running and observing and taking actions. 

```
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
    name: kuard
spec:
    replicas: 1 
    template: 
        metadata: 
            lables: 
                app: kuard
                version: "2"
        spec:
            containers:
                - name: kuard
                  image: "gcr.io/kuar-demo/kuard-amd64:2"
                  ports:
                    - containerPort: 80
            replicas: 3
```

``` kubectl apply -f juard-rs.yaml ```

``` kubectl describe rs kuard-rs ``` 

Scaling

``` kubectl scale replicasets kuard --replicas=4 ```

Autoscaling 

``` kubectl autoscale rs kuard --min=2 --max=5 --cpu-percent=80 ```

Get horizontalpodautoscaler
``` kubectl get hpa```
