# Kubernetes

With Kubernetes, declarative configuration is the main focus. It means that you will write down and submit the configuration to a service that makes sure that the configs become the actual state of your application.

The opposite to declarative configuration s the imperative configuration. There, the system's changes are made as a series of updates to the already running system.

Declarative configs have many advantages to imperative one. Code reviews for configuration changes as well as documenting the world for distributed teams. Additionally it is the basis for all self-healing behaviours happening in the clusters.

## Pod manifest
Pods are described in pod Manifest. The Manifest is just a text file representation of the Kubernetes API object. With Kubernetes, declarative configuration is the main focus. It means that you will write down and submit the configuration to a service that makes sure that the configs become the actual state of your application.


## Creating a pod
Creating with Imperative commands looks like this.

Create a pod with the command run.
`kubectl run kuard --image=gcr.io/kuar-demo/kuard-amd64:1`

See the availiabla pods with get pods.
`kubectl get pods`

Delete a pod
`kubectl delete deployments/kaurd`


Creating a pod using declarative configuration is done with manifest configuration: [kuard-pod.yaml](./kuard-pod.yaml).

To apply the manifest file just use:
`kubectl apply -f kuard-pod.yaml`

To see detailed description of the launched pod use :
`kubectl describe pods kuard`

to delete:
`kubectl delete pods/kuard`
`kubectl delete -f kuard-pod.yaml`


## Port forwarding
To expose a single pod use port-forwarding
`kubectl port-forward kuard 8080:8080`

## Logs
`kubectl logs kuard`
use `--previous` to see the previous logs if the container is continiously restarting

## Execute commands in coantienrs
Date command in kuard container
`kubectl exec kuard date`

Use `-it` to persiste the session.

## Copy files to and from containers
`Kubectl cp <pod-name>:/captures/capture3.txt ./capture3.txt`
Copiying files to caontainer is an antipatern.

## Health Checks
### Liveness probe
see [kuard-pod-health.yaml](./kuard-pod-health.yaml)

Liveness probe should get a success code 200 to show everything is ok, but if it excedes 400 then it shows otherwise. 
### Readyness probe
These probes are configured similar as liveness probes. And they are also used to prepare contianers with the load balancer. Readyness determines whether containers are running as they should be.

### Other types of probes
k8s supports:
- tcpSocket health checks - if socket oppenes, DB or other apps
- exec probes  - execute command and return zero exit code if successful

## Resource management 
Utilization metric is used to measure efficiency. UM is defined as the amount of a resource actively being used divided by the amount of a resource that has been purchased. If you have one core, and you app is using one thenth of that core, then utilisation is 10%.

With scheduling the K8s system enables more than 50% utilization.

- Resource requests - min amount of a resource required to run the app 
- Resource limits - max ammount of resource that app can consume 

```
apiVersion: v1
kind: Pod
metadata:
    name: kuard
spec:
    contianers:
        - image: gcr.io/kuar-demo/kuard-amd64:1
        name: kuard
        resources:
            requests:
                cpu: "500m"
                memory: "256Mi"
            limits:
                cpu: "1000m"
                memory: "256Mi"
            ports:
                - containerPort: 8080
                name: http
                protocol: TCP
                
```

Requested resources are per contianer. Noteper pod. Pod is the suim of all containing resources.

If the pod has only Requests defined and it is the only one running on the node it will get 100% of cpu cap. If two, theyu will share 50% and so on, untill they reach the limit defined by Requests. 

The memory limitation happens similarly as cpu, but the reduction is done by restarting the container!

With limits, THe Kernel in the os limits the usage of resources.

## Using volumes with Pods

- spec.volumes - array mounted to the pod
- volumeMounts - array mounted to the contianer
Not all contianers in the pod can get the same volumes
Two contianers can mount the same volume in the pod on different paths 


```
apiVersion: v1
kind: Pod
metadata:
    name: kuard
spec:
    volumes:
        - name: "kuard-data"
        hostPath:
            path: "/var/lib/kuard"
    containers:
        - image: gcr.io/kuar-demo/kuard-amd64:1
          name: kuard
          volumeMounts:
            - mountPath: "/data"
              name: "kuard-data"
          ports:
            - containerPort: 8080
              name: http
              protocol: TCP
                
```
Volumes are used for 
- communication/synchronizatrion
- cache
- persistent data
- mounting of the host filesystem 

Possibility to connect remote storage. NFS and SCSi protocols supported. 
