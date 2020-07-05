# Labels and annotations
Enable you top work in sets of things that map how you think about your application.

## Labels
Labels are key value pairs that can be attached to K8s objects. key: 63 chars, value, 253 chars 
Keys allow dashes(-), dots(.) and underscores(_).

`kubectl run alpaca-prod --image=gcr.io/kuar-demo/kuard-amd64:1 --replicas=2 --labels="ver=1, app=alpaca, env=prod"`

to see the deployments :
`kubectl get deployments --show-labels`

Updating:
`kubectl label deployments alpaca-test "canary=true"`

Exposing 

`kubectl get deployments -L canary`

Removing 

`kubectl label deployments alpaca-test "canary-"`

Selector:

- and
`kubectl get pods --selector="ver=2,app=bandicoot"`

-or
`kubectl get pods --selector="app in (alpaca,bandicoot)"`

- if label is set at all
`kubectl get pods --selector="cannary"`


## Annotations
A way to store metadata to purpose assisting tools and libraries. 

Annotations are used to pass information on usage, starting, stopping, scheduling policy, git hashes, build release or image information etc.
