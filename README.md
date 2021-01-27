# Kubernetes in Action 2nd
[K8s](https://kubernetes.io/)
[K8s API Reference](https://kubernetes.io/docs/reference/)
---

## API

- /apis/authorization.k8s.io/v1/subjectaccessreviews (indicates whether the subject is authorized to perform the operation specified in the request body.)
- /apis/apps/v1/namespaces/default/deployments/mydeploy
- **Main parts of an object**
    1. Type Metadata
    2. Object Metadata
    3. Spec
    4. Status
- Commands

```shell
$ oc get nodes
$ oc get node {{nome_name_here}} -o yaml
$ oc proxy (to view over browser)
$ oc explain nodes
```

## Pods

- pod is a co-located group of containers and represents the basic building block in Kubernetes;
- you always deploy and operate on a pod of containers;
- when a pod does contain multiple containers, all of them are always run on a single worker node—it never spans multiple worker nodes, it never spans multiple worker nodes;

## Pods Commands with OKD (pt.1)

```shell
$ oc port-forward quarkus-hello-pod 8080:8080
$ oc logs quarkus-hello-pod -c <container-name>  
$ oc get pods --show-labels -L creation_method,env 
$ oc label po quarkus-hello-pod env=debug --overwrite
$ oc get po -l creation_method=manual
$ oc get po -l '!env'
$ oc delete po quarkus-hello-pod
$ oc delete po -l creation_method=manual
$ oc delete po --all
$ oc run quarkus-hello --image=douglasdb/quarkus-hello:latest --port=8080 --generator=run/v1
```

## Minishift

1. minishift oc-env
2. eval $(minishift oc-env)


## Minimal Kubernetes Deploy Steps

1. Deployment - Manages one or more replicas of your application
2. Containers (Pod) - Each instance of your application runs in a pod
3. Service - Exposes your application instances to the world

Although pods may contain several, it’s not uncommon for a pod to contain just a single container. When a pod has multiple containers, all of them run on the same worker node, a single pod instance never spans multiple nodes.
A container use its own namespaces, but it can also share some with other containers. A pod can also be configured to use a single PID namespace for all its containers, which makes them share a single process tree, but you must explicitly enable this for each pod individually; you typically run only one application in each pod, you should divide them so that each pod runs only closely related application processes, e.g imagine a simple system composed of a front-end web server and a back-end database. I’ve already explained that the front-end server and the database shouldn’t run in the same container, **this isn’t the best approach**. But we can consider of a primary process and one or more processes that complement the operation of the primary process, this means sidecar containers in the same Pod, when use side cars? Ask yourself

* Do these containers have to run on the same host
* Do I want to manage them as a single unit
* Do they form a unified whole instead of being independent components
* Do they have to be scaled together
* Can a single node meet their combined resources needs


## Pods Commands with OKD (pt.2)

* OC Dry Run (true|false) true means to output definition instead of create object via the API.

```shell
$ oc apply -f kubia-pod.yaml
$ oc get po kubia -o yaml
$ kubeclt get po kubia -o yaml
$ oc run kubia --image=douglasdb/quarkus-hello:latest --dry-run=true -o yaml > kubia-dry-run-manifest.yaml
$ kubectl run kubia --image=douglasdb/quarkus-hello:latest --dry-run=client -o yaml > kubia-dry-run-manifest.yaml
$ oc get pod kubia
$ kubeclt get pod kubia
$ oc get pod kubia -o wide
$ kubeclt get pod kubia -o wide
$ oc run --image=tutum/curl -it --restart=Never --rm {{POD_NAME}} curl {{IP_POD}}:{{CONTAINER_PORT}}
$ $ oc port-forward {{POD_NAME}} 8080:8080
$ oc logs {{POD_NAME}} -f or (--follow)
$ oc logs {{POD_NAME}} --timestamps=true
$ oc logs {{POD_NAME}} --since=2m
$ oc logs {{POD_NAME}} --since-time={{DATETHOURZ}}
$ oc logs {{POD_NAME}} --tail
$ oc cp /path/from/file {{pod}}:path/in/container
$ oc exec {{pod}} --ps aux
$ kubectl exec {{pod}} -- curl -s localhost:8080
$ kubectl exec -it {{pod}} -- bash
$ kubectl attach
$ kubectl port-forward kubia-ssl 8080 8443 9901
$ kubectl exec -it kubia-ssl {{POD_NAME}} -c envoy {{CONTAINER}} -- bash
$ kubectl delete po {{POD_NAME_A}} {{POD_NAME_B}}
$ kubectl delete po {{POD_NAME}} --wait=false
$ kubectl delete -f {{MANIFEST_FILE.yaml}}
$ kubectl delete po --all
$ kubectl delete all --all
$ kubectl {{OBJECT_KIND}}, delete all --all
$ kubectl get po {{POD_NAME}} -o yaml | grep phase
$ kubectl describe po kubia
$ kubectl get events -w
$ kubectl get pods -w
```

* Logs usually are available in /var/log/containers on the node that runs the container

## Restart policy of Pods

* Note Surprisingly, the restart policy is configured at the pod level and applies to all its containers. It can’t be configured for each container individually.

1. Always: Container is restarted regardless of the exit code the process terminates with
2. OnFailure: Container is restarted only if the process terminates with a non-zero exit code.
3. Never: Container is never restarted regardless os the exit code

## Pods Phase

```shell
$ kubectl get po {{POD_NAME}} -o yaml | grep phase
$ kubectl describe {{POD_NAME}}
```

## Containers Healthy

```shell
$ kubectl get pods -w
$ kubectl get events -w
```