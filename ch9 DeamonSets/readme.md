# DeamonSets

Replicate a set of Pods on every node within the cluster. DeamonSet ensures a copy of a pod is running  across a set of nodes in a Kubernetes cluster . DS are used to deploy system deamons such as log collectors or monitoring agents, that must run on every node. 


DeamonSet should be used when a single copy of your app must run on all or subset of nodes in the cluster. 

If a new node is connected to the cluster, DeamonSet notices that is missing a Pod and it updates the node.

```
apiVersionL extensions/v1beta1
kind: DeamonSet
metadata:
    labels:
        app: nginx
        ssd: "true"
    name: nginx-fast-storage
spec: 
    template:
        metadata:
        labels:
            app: fluentd
        spec:
            template: 
                metadata: 
                    labels:
                        app:nginx
                        ssd: "true"
                spec:
                    nodeSelector:
                        ssd: "true"
                    contaners: 
                    - name: fluentd
                      image: fluent/fluentd:v014.10
```

Deamonsets willl create pods on every cluster unless a node selector is used. 

## Limiting DeamonSets

Adding labels to a node: 

```kubectl label nodes k0-default-pool-35609c18-z7tb ssd=true```

``` kubectl get nodes --selector ssd=true```

## Updating 

spec.updateStrategy.type

- spec.inReadySeconds
- spec.updateStrategy.rollingUpdate
- spec.updateStrategy.rollingUpdate.maxUnavailiable


