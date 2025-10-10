# Multi Container Pod Kubernetes - Sidecar vs Init Container

A Pod is the smallest deployable unit in Kubernetes.
Normally, a Pod runs a single container (like one nginx or one redis).

But sometimes, you need multiple containers inside the same Pod — that’s called a multi-container Pod.

**All containers in the same Pod:**
 - Run on the same Node
 - Share the same network namespace → same IP, same ports
 - Can share storage volumes
 - Can communicate via localhost


There can be two pateerns : 

**sidecar container :** Helper container adds functionality (logging, monitoring, proxy, etc.) 

**init container :** Special container that runs before main containers start 


