# Charmed Kubernetes on OpenStack
- [Charmed Kubernetes on OpenStack](#charmed-kubernetes-on-openstack)
  - [OpenStack integrator](#openstack-integrator)
  - [Installing](#installing)
  - [Using Octavia Load Balancers](#using-octavia-load-balancers)
  - [LoadBalancer-type Pod Services](#loadbalancer-type-pod-services)
  - [Reference:](#reference)

Charmed Kubernetes will run seamlessly on OpenStack. With the addition of the *openstack-integrator*, your cluster will also be able to directly use OpenStack native features.

## OpenStack integrator

The *openstack-integrator* charm simplifies working with Charmed Kubernetes on OpenStack. Using the credentials provided to Juju, it acts as a proxy between Charmed Kubernetes and the underlying cloud, granting permissions to dynamically create, for example, Cinder volumes.

## Installing

When installing Charmed Kubernetes [using the Juju bundle](https://ubuntu.com/kubernetes/docs/install-manual), you can add the openstack-integrator at the same time by using the following overlay file ([download it here]()https://raw.githubusercontent.com/charmed-kubernetes/bundle/main/overlays/openstack-overlay.yaml):

  ```console
  description: Charmed Kubernetes overlay to add native OpenStack support.
  applications:
    openstack-integrator:
      annotations:
        gui-x: "600"
        gui-y: "300"
      charm: openstack-integrator
      num_units: 1
      trust: true
  relations:
    - ['openstack-integrator', 'kubernetes-control-plane:openstack']
    - ['openstack-integrator', 'kubernetes-worker:openstack']
  ```

To use the overlay with the Charmed Kubernetes bundle, specify it during deploy like this:

  ```console
  juju deploy charmed-kubernetes --overlay ~/path/openstack-overlay.yaml --trust
  ```

... and remember to fetch the configuration file!

  ```console
  juju scp kubernetes-control-plane/0:config ~/.kube/config
  ```

For more configuration options and details of the permissions which the integrator uses, please see the [charm docs](https://charmhub.io/containers-openstack-integrator?_ga=2.45797217.458577141.1655983494-1295280413.1655201211).

## Using Octavia Load Balancers

There are two ways in which Octavia load balancers can be used with Charmed Kubernetes: load balancers automatically created by Kubernetes for Services which sit in front of *Pods* and are defined with *type=LoadBalancer*, and as a replacement for the load balancer in front of the API server itself.

In either case, the load balancers can optionally have floating IPs (FIPs) attached to them to allow for external access.

## LoadBalancer-type Pod Services

To use Octavia for LoadBalancer type services in the cluster, you will also need to set the subnet-id config to the appropriate tenant subnet where your nodes reside, and if desired, the floating-network-id config to whatever network you want FIPs created in. See the [Charm config docs](https://ubuntu.com/kubernetes/docs/charm-openstack-integrator#configuration) for more details.

As an example of this usage, this will create a simple application, scale it to five pods, and expose it with a *LoadBalancer-type* Service:

  ```console
  kubectl create deployment hello-world --image=gcr.io/google-samples/node-hello:1.0
  kubectl scale deployment hello-world --replicas=5
  kubectl expose deployment hello-world --type=LoadBalancer --name=hello --port 8080
  ```

You can verify that the application and replicas have been created with:

  ```console
  kubectl get deployments hello-world
  ```

Which should return output similar to:

  ```console
  NAME             READY   UP-TO-DATE   AVAILABLE   AGE
  hello-world      5/5     5            5           2m38s
  ```

To check that the service is running correctly:

  ```console
  kubectl get service hello
  ```

## Reference:

- [OpenStack Victoria Magnum user guides](https://docs.openstack.org/magnum/victoria/user/)
- [Magnum Quickstart guide](https://docs.openstack.org/magnum/latest/contributor/quickstart.html)