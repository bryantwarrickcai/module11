# Module 11
## Tutorial 1
### Question 1
Before the application is exposed as a Service, here was the output:
```
PS C:\Users\Bryant\Desktop> kubectl logs hello-node-c74958b5d-pnvqf
I0529 08:38:33.581370       1 log.go:195] Started HTTP server on port 8080
I0529 08:38:33.581627       1 log.go:195] Started UDP server on port  8081
```

After exposing the application as a Service and running the application multiple times, the output becomes like this:
```
PS C:\Users\Bryant\Desktop> kubectl logs hello-node-c74958b5d-pnvqf
I0529 08:38:33.581370       1 log.go:195] Started HTTP server on port 8080
I0529 08:38:33.581627       1 log.go:195] Started UDP server on port  8081
I0529 08:48:28.052374       1 log.go:195] GET /
I0529 08:48:28.104415       1 log.go:195] GET /
I0529 08:49:20.887368       1 log.go:195] GET /
I0529 08:49:20.925445       1 log.go:195] GET /
I0529 08:49:21.494394       1 log.go:195] GET /
```

**Explanation:**

Before running the Pod, the application shows the HTTP and UDP servers are started successfully on ports 8080 and 8081, respectively. However, there are no incoming requests yet. This is because the application is not exposed outside of the Pod, meaning that there is no way for external clients to reach it, and therefore, no requests were being made to it.

After exposing the application as a Kubernetes Service, it creates a stable endpoint which routes traffic to the Pod. External clients can now send HTTP requests to the Service's IP. When I made 5 HTTP requests to the application, each request is shown as a GET request in the logs, confirming that the application is now being accessed through the service.

The more times you open the app, the more HTTP GET requests are created, and more logs are created.

### Question 2
The `-n` option specifies the Kubernetes namespace to retrieve from. Without the `-n` option, it will look in the default namespace, unless another one is explicitly set.

When you create resources (pods or services) without specifying a namespace, it will by default create them in the `default` namespace. The `kube-system` namespace is a special namespace used to run and manage the core system components and infrastructure of the Kubernetes cluster itself, holding essential system pods and services required for Kubernetes to operate properly.

The output with the `-n kube-system` option did not list the pods/services explicitly created because it gives pods created on the `kube-system` namespace, not the `default` namespace, whereas we created our own pods and services in the `default` namespace.

## Tutorial 2
### Question 1
In the Rolling Update strategy, old pods are gradually updated with newer ones, ensuring that some instances of the application still remain available at all times while new pods are being created. The Recreate strategy, on the other hand, terminates all existing pods in the old version before creating new ones in the updated version. It is used if the application cannot handle multiple versions running concurrently.

### Question 2
First write the following command in PowerShell:
```
kubectl patch deployment spring-petclinic-rest -p '{"spec": {"strategy": {"type": "Recreate"}}}'
```

This command updates the `spec.strategy.type` field of the Deployment to `"Recreate"`. By default, it is set to `"RollingUpdate"`.

However, this method doesn't work for me (using escaped double quotes inside double quotes also doesn't work).

So another method is:

Run `kubectl edit deployment spring-petclinic-rest`

This will open a YAML file containing the configurations. Find the `strategy` field inside `spec`, and modify it so that it says `type: Recreate`.
Contents of the file should be:
```
...
spec:
  ...
  strategy:
    type: Recreate
  ...
...
```

After that, run `kubectl get deployment spring-petclinic-rest -o jsonpath='{.spec.strategy.type}'` to verify the deployment strategy type (make sure that it outputs `Recreate`.)

After that, simply run `kubectl set image deployments/spring-petclinic-rest spring-petclinic-rest=docker.io/springcommunity/spring-petclinic-rest:3.2.1` as usual.

The update status can be verified using `kubectl rollout status deployments/spring-petclinic-rest`.

### Question 3
The files can be seen in `deployment-recreate.yaml` and `services-recreate.yaml`.

### Question 4
Using manifest files directly define the desired state of the application. This is called declarative configuration. Using `kubectl set image` (manually), on the other hand, is imperative, as you're changing the state directly (one field at a time) without a record of the full configuration. Using manifests also make it able to be stored in Git, allowing change tracking, rollbacks, and code reviews. Using the manual method does not leave any record of the changes, unless manually documented. Using a manifest file also ensures consistency in the deployment, as using manual commands may risk inconsistencies (such as forgetting to update a label). Manifest files also allow for easier automation with CI/CD pipelines, as imperative commands require extra scripting.
