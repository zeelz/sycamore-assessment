## DevOps troubleshooting

### Root Cause Analysis (RCA)

Listing pods with `kubectl get pods` shows a `CrashLoopBackOff` status.
Further investigation with `kubectl describe pod/sycamore-api-85b747dc88-xlw6s` shows a `Waiting` current state and the last state as `Terminated` due to `OOMKilled`

OOMKilled happened when the container was forcibly terminated because it exceeded assigned memory limit.

Looking at the manifest it is obvious the nodejs pod crashes due to memory overload causing the pod to go into OOMKilled state.

The nodejs container creates an array and tried to add 1million items every 100 milliseconds. For this reason the memory usage grows until it hits the 64Mi resource limits set by the container.

The pod goes into CrashLoopBackOff state after it retries to restart but keeps getting the same error.

At either state, OOMKilled or CrashLoopBackOff, the pod exposed by a service is unreachable, causing a 502 gateway error


### The Fix


Infinitely adding 1million items to an array every 100 milliseconds is bad and dangerous design. This is because even with a high memory limit, at some point memory would reach capacity and OOMKilled would occur.

1. The first fix therefore is a better design. One that does not infinitely append items to array.

2. A recommendation would be to increase the memory limits.

3. To prevent 502 error, include health check in the manifest. The pod would not receive traffic unhealthy or not ready. For that to work the app is started on port health check would listen for.
