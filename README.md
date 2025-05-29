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
