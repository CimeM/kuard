# Deployments

Deployments object exists to manage the release of new version

This is actually creating a deployment object under the hood. 
``` kubectl run nginx --image=nginx:1.7.12```

``` kubectl get deployments```

Deployments manage ReplicaSets. this relationship is defined by lables. 
``` kubectl get deployments nginx -o jsonpath --template {.spec.selector.matchLabels}```

Resize the Deployment using the scale command:
```kubectl scale deployments nginx --replicas=2```

You cannot scale replicasets while Deployments object exists. The system will self-heal...

Exporting deployments in yaml file:
```kubectl get deployments nginx --export -o yaml > nginx-deployment.yaml```

```
apiVersion: extensions/v1beta1
kind: Deployment
metadata: 
    annotations:
        deployment.kubernetes.io/revision: "1"
    labels:
        run: nginx
    name: nginx
    namespace: default
spec:
    replicas: 2
    selector:
        matchLabels:
            run: nginx
    strategy:
        rollingUpdate
            maxSurge: 1
            maxUnavailiable: 1
        type: RollingUpdate
    template: 
        metadata:
            labels:
                run: nginx
        spec:
            contianers:
            - image: nginx:1.7.12
              imagePullPolicy: Always
            dnsPolicy: ClusterFirst
            restartPolicy: Always
```

Making changes to the deployment object calls for applying it afterwards with:
``` kubectl apply -f nginx-deployment.yaml```

Monitor the rolout with:

``` kubectl rollout status deploymens nginx```
See the history:

```kubectl rollout history deployment nginx --revision=2```

Undo the last rollout:
```kubectl rollout undo deployments nginx```
or
```kubectl rollout undo deployments nginx --to-revision=3```

## Deployment strategies

- RollingUpdate
- Recreate