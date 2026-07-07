# Kubernetes (K8s) : Best Practices & Architecture

## 1. Resource Management (CPU & Memory)
- **The OOM Killer Rule:** Never deploy a pod without explicit `resources.limits` and `resources.requests`.
- **Memory:** Set memory `requests` equal to `limits` for critical backend services to guarantee a high Quality of Service (QoS) and prevent the node from evicting the pod during memory pressure.
- **CPU:** Set CPU `requests` to what the application needs to boot and run normally. CPU `limits` can be higher to allow bursting, but be aware of CPU throttling if limits are hit.

## 2. Health & Liveness Probes
- **Startup vs. Liveness:** Use `startupProbe` for legacy applications that take a long time to boot. Use `livenessProbe` strictly to detect deadlocks (e.g., an infinite loop). If a `livenessProbe` fails, K8s restarts the pod.
- **Readiness:** Use `readinessProbe` to check if the app can accept traffic (e.g., verifying database connectivity). If it fails, K8s stops sending HTTP requests to it, but does not kill the pod.

## 3. Immutability & State
- **Stateless Pods:** Treat pods as ephemeral and disposable. Never store local state, uploaded files, or SQLite databases directly inside a standard Pod's filesystem. They will be deleted upon restart.
- **Persistent Storage:** If state is mandatory, use StatefulSets combined with PersistentVolumeClaims (PVC).

## 4. Security Context
- **Non-Root Execution:** Always enforce `runAsNonRoot: true` in the pod's `securityContext`. Containers running as root pose a critical security risk if a breakout vulnerability is exploited.