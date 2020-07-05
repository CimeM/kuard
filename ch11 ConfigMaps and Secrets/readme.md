# ConfigMaps and Secrets



## ConfigMaps 
ConfigMaps are used to provide configuration information for workloads.

ConfigMap is combined with the Pod right before it is run.  Container image and the the pod definition itself can be reused across many apps by just changing the ConfigMap.

``` kubectl create configmap my-config --from-file=my-config.txt --from -literal=extra-param=extra-value --from-literal=another-param=another-value```

To display: 
```kubectl get configmaps my-config -o yaml``


Usage: 
- Filesystem - ConfigMap is connected to the pod
- environment variable - can be used dynamically set the env var
- by kubelet when pulling images for the pod 

```
...
spec:
  containers:
    - name: test-container
      image: gcr.io/kuar-demo/kuard-amd64:1
      imagePullPolicy: Always
      command:
        - "/kuard"
        - "$(EXTRA_PARAM)"
      env:
        - name: ANOTHER_PARAM
          valueFrom:
            configMapKeyRef:
              name: my-config
              key: another-param
...
```

This is a pod manifest file. In here we draw from ConfigMap  by using configMapKeyRef.name and configMapKeyRef.key

Same goes for volumes:

```
volumes:
  - name: config-volume
    configMap:
    name: my-config
```


# Secrets
Creating a secret of two data elements. 

``` kubectl create secret generic kuard-tls --from-file=kuard.crt --from-file=kuard.key```
- the files contiain keys.

To view the data points
``` kubectl describe secrets kuard-tls```

## Portability
Using secrets volumes. Volumes are exposed to pods. These volumes are managed by kubelet and are created at pod creation time.
mounting the juard-tls volume to /tls results in the following files: /tls/cert.pem and /tls/key.pem

To reference the keys:
```
... [pod Manifest file ]
spec:
    containers:
        - name: kuard-tls
          image: gcr.io/kuar-demo/kuard-amd64:1
          imagePullPolicy: Always
          volumeMounts:
          - name: tls-certs
            mountPath: "/tls"
            readOnly: true
    volumes:
        - name: tls-certs
          secret:
            secretName: kuard-tls
...
```

# Adding a secret to contianer

1. create a secret
2. modify pod Manifest to add a volume under `.spec.volumes[]`. You can name the volume anything. `.spec.volumes[].secret.secretName` field equal to the name of the Secret object
3. Add a `.spec.containers[].volumeMounts[]` to each container that needs the secret. Specify `.spec.containers.volumeMounts[].mountPath` to an unused dir name where you would like the secret to appear
4. Modify your image or command line  so that the program looks for files in that dir. Each key in the secret `data` map becomes the filename under `mountPath`

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo"
      readOnly: true
  volumes:
  - name: foo
    secret:
      secretName: mysecret
```


# Updating 

You can update a configMap or secret and have it reflected in running programs. There is no need to restart if the application is configed to read the config valuses. 

There is no mechanism that tells application that config has changed. It is up to the app to detect and reload them. 