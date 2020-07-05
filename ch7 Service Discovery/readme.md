# Service discovery 


```alpaca-prod.default.svc.cluster.local```

alpaca-prod - name of the service
default - name of the namespace 
svc - this is a service 
cluster.local - the base domain name of the cluster 

When refering to the service in current namespace you can just use `alpaca-prod`

# Readyness Probe

Readyness probe is a great way to check the contianer status but also a way to gracefully exit the ingress loop.

# Endpoints

Endpoints show the IP address of the contianer of a service. The feature may be usefull for the applications that were not written to run in kubernetes and still need to refer to services using IPs. 

# Manual service discovery

To get the services and their labels 
```kubectl get pods -o wide --show-labels```

to select by labels and output their addresses

```kubectl get pods -o wide --selector=app=alpaca,env=prod```

Using and updating labels correctly makes service discovery faster. But updating lables can be tricky, this is why Service object was created.

# kube-proxy and Cluster IPS

kube proxy updates IP tables, that are held in the kernel. If one service is failing (failing readyness check), the proxy updates the tables by removing the entry.

ENV  variables introduce complexity to large applications because the creation process has to accomodate the services first, before the pods thet reference them. Using just env variables seem strange to many users... DNS is a better option.


# DNS
- A/AAAA records - Normal (not headless) resolves an address (including cluster) to an IP, headless -> (without cluster address)   
- SRV records - created for named ports -> part of normall headless service
