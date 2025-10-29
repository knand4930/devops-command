# Complete Kubernetes Commands Reference Guide

## kubectl Commands

### Cluster Management

#### `kubectl cluster-info`
Displays cluster information including the Kubernetes master and services addresses.
```bash
kubectl cluster-info
kubectl cluster-info dump  # Detailed cluster state dump
```

#### `kubectl version`
Shows the client and server Kubernetes version.
```bash
kubectl version --short
kubectl version --client  # Only client version
```

#### `kubectl config`
Manages kubeconfig files for cluster authentication and context switching.
```bash
kubectl config view                    # Show merged kubeconfig
kubectl config get-contexts            # List all contexts
kubectl config current-context         # Display current context
kubectl config use-context <name>      # Switch to a different context
kubectl config set-context <name>      # Set context properties
kubectl config delete-context <name>   # Remove a context
kubectl config set-cluster <name>      # Configure cluster entry
kubectl config set-credentials <name>  # Set user credentials
```

### Resource Creation and Management

#### `kubectl create`
Creates resources from files or command line arguments. Imperative command.
```bash
kubectl create -f <file.yaml>                    # Create from file
kubectl create deployment <name> --image=<img>   # Create deployment
kubectl create service clusterip <name>          # Create ClusterIP service
kubectl create configmap <name> --from-file=<>   # Create ConfigMap
kubectl create secret generic <name>             # Create generic secret
kubectl create namespace <name>                  # Create namespace
kubectl create serviceaccount <name>             # Create service account
kubectl create job <name> --image=<image>        # Create job
kubectl create cronjob <name> --image=<>         # Create CronJob
```

#### `kubectl apply`
Applies configuration to resources. Declarative approach that tracks changes.
```bash
kubectl apply -f <file.yaml>           # Apply single file
kubectl apply -f <directory>           # Apply all files in directory
kubectl apply -k <kustomization-dir>   # Apply Kustomize directory
kubectl apply --recursive -f <dir>     # Apply recursively
kubectl apply --dry-run=client         # Preview changes without applying
kubectl apply --server-side            # Server-side apply
```

#### `kubectl delete`
Deletes resources by filename, resource type, name, or label selector.
```bash
kubectl delete -f <file.yaml>              # Delete from file
kubectl delete pod <name>                  # Delete specific pod
kubectl delete pods --all                  # Delete all pods
kubectl delete pods -l <label>=<value>     # Delete by label
kubectl delete namespace <name>            # Delete namespace
kubectl delete deployment <name>           # Delete deployment
kubectl delete --grace-period=0 --force    # Force immediate deletion
```

#### `kubectl edit`
Edits resources directly on the server using default editor.
```bash
kubectl edit deployment <name>         # Edit deployment
kubectl edit svc <name>                # Edit service
kubectl edit configmap <name>          # Edit ConfigMap
```

#### `kubectl patch`
Updates specific fields of a resource using strategic merge patch, JSON merge patch, or JSON patch.
```bash
kubectl patch deployment <name> -p '{"spec":{"replicas":3}}'
kubectl patch pod <name> --type='json' -p='[{"op": "replace", "path": "/spec/containers/0/image", "value":"nginx:latest"}]'
```

#### `kubectl replace`
Replaces a resource by filename or stdin. Must provide complete resource definition.
```bash
kubectl replace -f <file.yaml>
kubectl replace --force -f <file.yaml>  # Delete and recreate
```

### Resource Inspection

#### `kubectl get`
Lists one or many resources. Highly versatile command.
```bash
kubectl get pods                           # List all pods
kubectl get pods -o wide                   # Detailed pod info
kubectl get pods -o yaml                   # YAML output
kubectl get pods -o json                   # JSON output
kubectl get pods --all-namespaces          # All namespaces
kubectl get pods -n <namespace>            # Specific namespace
kubectl get pods -l <label>=<value>        # Filter by label
kubectl get pods --field-selector          # Filter by field
kubectl get all                            # Get common resources
kubectl get pods --watch                   # Watch for changes
kubectl get pods --sort-by=.metadata.name  # Sort results
kubectl get pods --selector="app=nginx"    # Label selector
```

#### `kubectl describe`
Shows detailed information about a specific resource including events.
```bash
kubectl describe pod <name>
kubectl describe deployment <name>
kubectl describe node <name>
kubectl describe service <name>
kubectl describe pv <name>
kubectl describe pvc <name>
```

#### `kubectl logs`
Prints logs from containers in a pod.
```bash
kubectl logs <pod-name>                       # Pod logs
kubectl logs <pod-name> -c <container-name>   # Specific container
kubectl logs <pod-name> --previous            # Previous terminated container
kubectl logs <pod-name> --follow              # Stream logs
kubectl logs <pod-name> --tail=50             # Last 50 lines
kubectl logs <pod-name> --since=1h            # Last hour of logs
kubectl logs -l <label>=<value>               # Logs from all matching pods
kubectl logs <pod-name> --timestamps          # Include timestamps
```

#### `kubectl exec`
Executes commands in a container.
```bash
kubectl exec <pod-name> -- <command>                    # Execute command
kubectl exec -it <pod-name> -- /bin/bash                # Interactive shell
kubectl exec <pod-name> -c <container-name> -- <cmd>    # Specific container
kubectl exec <pod-name> -- env                          # List environment variables
```

#### `kubectl port-forward`
Forwards local port to a pod port for debugging/accessing services.
```bash
kubectl port-forward <pod-name> 8080:80              # Forward local 8080 to pod 80
kubectl port-forward svc/<service-name> 8080:80      # Forward to service
kubectl port-forward deployment/<name> 8080:80       # Forward to deployment
kubectl port-forward --address 0.0.0.0 <pod> 8080:80 # Listen on all interfaces
```

#### `kubectl top`
Displays resource usage (CPU/Memory). Requires Metrics Server.
```bash
kubectl top nodes                     # Node resource usage
kubectl top pods                      # Pod resource usage
kubectl top pods -n <namespace>       # Specific namespace
kubectl top pods --containers         # Container-level metrics
kubectl top pods -l <label>=<value>   # Filter by label
```

### Scaling and Updates

#### `kubectl scale`
Scales a deployment, replica set, or replication controller.
```bash
kubectl scale deployment <name> --replicas=3
kubectl scale --replicas=5 -f <file.yaml>
kubectl scale deployment <name> --current-replicas=2 --replicas=3  # Conditional scaling
```

#### `kubectl autoscale`
Creates a HorizontalPodAutoscaler for automatic scaling.
```bash
kubectl autoscale deployment <name> --min=2 --max=10 --cpu-percent=80
kubectl autoscale rc <name> --max=5 --cpu-percent=60
```

#### `kubectl rollout`
Manages rollout of a resource (deployment, daemonset, statefulset).
```bash
kubectl rollout status deployment/<name>         # Check rollout status
kubectl rollout history deployment/<name>        # View rollout history
kubectl rollout undo deployment/<name>           # Rollback to previous revision
kubectl rollout undo deployment/<name> --to-revision=2  # Rollback to specific revision
kubectl rollout pause deployment/<name>          # Pause rollout
kubectl rollout resume deployment/<name>         # Resume rollout
kubectl rollout restart deployment/<name>        # Restart deployment
```

#### `kubectl set`
Sets specific features on objects.
```bash
kubectl set image deployment/<name> <container>=<image>    # Update image
kubectl set resources deployment <name> -c=<container> --limits=cpu=200m,memory=512Mi
kubectl set env deployment/<name> KEY=VALUE               # Set environment variable
kubectl set serviceaccount deployment <name> <sa-name>    # Set service account
```

### Debugging and Troubleshooting

#### `kubectl attach`
Attaches to a running container.
```bash
kubectl attach <pod-name>
kubectl attach <pod-name> -c <container-name>
kubectl attach <pod-name> -i                    # Interactive mode
```

#### `kubectl cp`
Copies files and directories to/from containers.
```bash
kubectl cp <pod-name>:/path/to/file ./local-file          # Copy from pod
kubectl cp ./local-file <pod-name>:/path/to/file          # Copy to pod
kubectl cp <pod-name>:/path/to/dir ./local-dir -c <cont>  # Specific container
```

#### `kubectl debug`
Creates debugging sessions (Alpha/Beta feature).
```bash
kubectl debug <pod-name> -it --image=busybox              # Debug with new container
kubectl debug node/<node-name> -it --image=ubuntu         # Debug node
kubectl debug <pod-name> --copy-to=<new-pod-name>         # Copy pod for debugging
```

#### `kubectl proxy`
Creates a proxy server between localhost and Kubernetes API server.
```bash
kubectl proxy                              # Start proxy on port 8001
kubectl proxy --port=8080                  # Custom port
kubectl proxy --address='0.0.0.0'          # Accept connections from any address
kubectl proxy --accept-hosts='^.*$'        # Accept all hosts
```

#### `kubectl wait`
Waits for a specific condition on one or more resources.
```bash
kubectl wait --for=condition=Ready pod/<pod-name>
kubectl wait --for=condition=Available deployment/<name>
kubectl wait --for=delete pod/<pod-name> --timeout=60s
```

### Resource Labeling and Annotation

#### `kubectl label`
Updates labels on resources.
```bash
kubectl label pods <pod-name> env=prod                # Add label
kubectl label pods <pod-name> env=dev --overwrite     # Update label
kubectl label pods <pod-name> env-                    # Remove label
kubectl label pods --all env=prod                     # Label all pods
```

#### `kubectl annotate`
Updates annotations on resources.
```bash
kubectl annotate pods <pod-name> description="my app"
kubectl annotate pods <pod-name> description-         # Remove annotation
kubectl annotate --overwrite pod <name> key=value     # Update annotation
```

### Advanced Commands

#### `kubectl api-resources`
Lists available API resources in the cluster.
```bash
kubectl api-resources                        # List all resources
kubectl api-resources --namespaced=true      # Only namespaced resources
kubectl api-resources --namespaced=false     # Only cluster-scoped resources
kubectl api-resources -o wide                # Include additional info
kubectl api-resources --api-group=apps       # Filter by API group
```

#### `kubectl api-versions`
Lists available API versions on the server.
```bash
kubectl api-versions
```

#### `kubectl explain`
Documentation of resources and their fields.
```bash
kubectl explain pods                         # Pod documentation
kubectl explain pods.spec                    # Pod spec documentation
kubectl explain pods.spec.containers         # Container spec
kubectl explain deployment.spec.replicas     # Specific field
kubectl explain pods --recursive             # All fields recursively
```

#### `kubectl diff`
Shows differences between current configuration and applied configuration.
```bash
kubectl diff -f <file.yaml>
kubectl diff -f <directory>
```

#### `kubectl drain`
Drains a node in preparation for maintenance.
```bash
kubectl drain <node-name>                              # Drain node
kubectl drain <node-name> --ignore-daemonsets          # Ignore DaemonSets
kubectl drain <node-name> --delete-emptydir-data       # Delete emptyDir data
kubectl drain <node-name> --force                      # Force drain
kubectl drain <node-name> --grace-period=60            # Grace period
```

#### `kubectl cordon`
Marks a node as unschedulable.
```bash
kubectl cordon <node-name>
```

#### `kubectl uncordon`
Marks a node as schedulable.
```bash
kubectl uncordon <node-name>
```

#### `kubectl taint`
Updates taints on nodes.
```bash
kubectl taint nodes <node-name> key=value:NoSchedule         # Add taint
kubectl taint nodes <node-name> key=value:NoExecute          # Evict pods
kubectl taint nodes <node-name> key:NoSchedule-              # Remove taint
kubectl taint nodes <node-name> key-                         # Remove all with key
```

#### `kubectl certificate`
Modifies certificate resources (approve, deny).
```bash
kubectl certificate approve <csr-name>
kubectl certificate deny <csr-name>
```

#### `kubectl auth`
Inspects authorization and permissions.
```bash
kubectl auth can-i create pods                              # Check if you can create pods
kubectl auth can-i create pods --as=user                    # Check as different user
kubectl auth can-i '*' '*'                                  # Check if cluster admin
kubectl auth can-i list pods --all-namespaces               # Check specific action
kubectl auth reconcile -f <rbac.yaml>                       # Reconcile RBAC
kubectl auth whoami                                         # Show current user info
```

#### `kubectl convert`
Converts config files between different API versions.
```bash
kubectl convert -f <old-file.yaml>                          # Convert to latest version
kubectl convert -f <file.yaml> --output-version apps/v1     # Convert to specific version
```

#### `kubectl kustomize`
Builds kustomization targets from a directory or URL.
```bash
kubectl kustomize <kustomization-directory>                 # Build kustomization
kubectl kustomize <dir> -o <output.yaml>                    # Output to file
kubectl kustomize --enable-helm                             # Enable Helm chart inflation
```

#### `kubectl options`
Prints list of global command-line options.
```bash
kubectl options
```

#### `kubectl plugin`
Provides utilities for interacting with plugins.
```bash
kubectl plugin list                                         # List all available plugins
```

#### `kubectl events`
Lists events in the cluster.
```bash
kubectl events                                              # List events
kubectl events --watch                                      # Watch events
kubectl events --for pod/<pod-name>                         # Events for specific resource
kubectl events --types=Warning,Error                        # Filter by event type
kubectl events --all-namespaces                             # All namespaces
```

#### `kubectl alpha`
Experimental commands (varies by version).
```bash
kubectl alpha events                                        # Alpha events command
kubectl alpha debug                                         # Alpha debug features
```

#### `kubectl run`
Creates and runs a particular image in a pod (primarily for testing).
```bash
kubectl run nginx --image=nginx                             # Run pod
kubectl run nginx --image=nginx --port=80                   # Expose port
kubectl run nginx --image=nginx --dry-run=client -o yaml    # Generate YAML
kubectl run -it busybox --image=busybox -- sh               # Interactive pod
kubectl run nginx --image=nginx --restart=Never             # Run as Job
kubectl run nginx --image=nginx --env="VAR=value"           # With env var
```

#### `kubectl expose`
Exposes a resource as a new Kubernetes service.
```bash
kubectl expose pod <pod-name> --port=80 --target-port=8080
kubectl expose deployment <name> --type=NodePort --port=80
kubectl expose deployment <name> --type=LoadBalancer
kubectl expose rc <name> --port=80 --target-port=8000
```

---

## kind Commands

### Cluster Management

#### `kind create cluster`
Creates a new Kubernetes cluster using kind.
```bash
kind create cluster                                    # Default cluster named "kind"
kind create cluster --name <cluster-name>              # Named cluster
kind create cluster --config <config.yaml>             # With configuration file
kind create cluster --image kindest/node:v1.25.0       # Specific K8s version
kind create cluster --wait 5m                          # Wait for control plane
kind create cluster --retain                           # Retain nodes on failure
```

**Configuration file example:**
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

#### `kind delete cluster`
Deletes an existing kind cluster.
```bash
kind delete cluster                           # Delete default cluster
kind delete cluster --name <cluster-name>     # Delete named cluster
kind delete clusters --all                    # Delete all clusters
```

#### `kind get clusters`
Lists all existing kind clusters.
```bash
kind get clusters
```

#### `kind get nodes`
Lists nodes in a specific cluster.
```bash
kind get nodes                          # Nodes in default cluster
kind get nodes --name <cluster-name>    # Nodes in named cluster
```

#### `kind get kubeconfig`
Prints the kubeconfig for a cluster.
```bash
kind get kubeconfig                              # Default cluster
kind get kubeconfig --name <cluster-name>        # Named cluster
kind get kubeconfig --internal                   # Internal cluster address
```

### Image Management

#### `kind load docker-image`
Loads a Docker image from the host into the cluster nodes.
```bash
kind load docker-image <image-name>                    # Load into default cluster
kind load docker-image <image-name> --name <cluster>   # Load into named cluster
kind load docker-image nginx:latest my-app:v1.0        # Load multiple images
```

#### `kind load image-archive`
Loads images from a tar archive.
```bash
kind load image-archive <archive.tar>
kind load image-archive <archive.tar> --name <cluster>
```

### Building and Exporting

#### `kind build node-image`
Builds the node image for kind from Kubernetes source.
```bash
kind build node-image                               # Build from current directory
kind build node-image --image <custom-image-name>   # Custom image name
kind build node-image --base-image <base>           # Custom base image
kind build node-image --kube-root <path>            # Path to K8s source
```

#### `kind export logs`
Exports logs from the cluster.
```bash
kind export logs                             # Export to ./kind-logs
kind export logs <output-dir>                # Export to specific directory
kind export logs --name <cluster-name>       # From named cluster
```

### Utility Commands

#### `kind version`
Prints the kind version.
```bash
kind version
```

#### `kind completion`
Generates shell completion scripts.
```bash
kind completion bash                    # Bash completion
kind completion zsh                     # Zsh completion
kind completion fish                    # Fish completion
source <(kind completion bash)          # Load completion
```

---

## kubeadm Commands

### Cluster Initialization

#### `kubeadm init`
Initializes a Kubernetes control-plane node.
```bash
kubeadm init                                           # Basic initialization
kubeadm init --pod-network-cidr=10.244.0.0/16         # With pod network CIDR
kubeadm init --apiserver-advertise-address=<ip>       # Specify API server address
kubeadm init --control-plane-endpoint=<endpoint>      # HA control plane endpoint
kubeadm init --kubernetes-version=v1.25.0             # Specific K8s version
kubeadm init --config=<config.yaml>                   # From configuration file
kubeadm init --upload-certs                           # Upload certs for HA
kubeadm init --dry-run                                # Preview without applying
kubeadm init --skip-phases=<phases>                   # Skip specific phases
kubeadm init --ignore-preflight-errors=<errors>       # Ignore preflight checks
```

**Phases that can be executed separately:**
- preflight: Run pre-flight checks
- certs: Certificate generation
- kubeconfig: Generate kubeconfig files
- kubelet-start: Start kubelet
- control-plane: Set up control plane components
- etcd: Set up etcd
- upload-config: Upload kubeadm config
- upload-certs: Upload certificates
- mark-control-plane: Mark node as control-plane
- bootstrap-token: Generate bootstrap tokens
- kubelet-finalize: Finalize kubelet
- addon: Install addons

#### `kubeadm join`
Joins a node to the cluster (worker or control-plane).
```bash
# Worker node join
kubeadm join <control-plane-endpoint>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>

# Control plane join (HA)
kubeadm join <control-plane-endpoint>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash> --control-plane --certificate-key <key>

# Additional options
kubeadm join <endpoint> --token <token> --discovery-token-ca-cert-hash <hash> --apiserver-advertise-address=<ip>
kubeadm join --config=<config.yaml>                   # From configuration file
kubeadm join --dry-run                                # Preview without applying
kubeadm join --ignore-preflight-errors=<errors>       # Ignore preflight checks
```

### Token Management

#### `kubeadm token`
Manages bootstrap tokens for joining nodes.
```bash
kubeadm token list                                    # List all tokens
kubeadm token create                                  # Create new token
kubeadm token create --ttl 24h                        # Token with specific TTL
kubeadm token create --print-join-command             # Print complete join command
kubeadm token delete <token>                          # Delete token
kubeadm token generate                                # Generate token string
```

### Certificate Management

#### `kubeadm certs`
Manages cluster certificates.
```bash
kubeadm certs check-expiration                        # Check certificate expiration
kubeadm certs renew all                               # Renew all certificates
kubeadm certs renew apiserver                         # Renew specific certificate
kubeadm certs renew admin.conf                        # Renew admin kubeconfig
kubeadm certs renew controller-manager.conf           # Renew controller-manager
kubeadm certs renew scheduler.conf                    # Renew scheduler
kubeadm certs certificate-key                         # Generate certificate key
```

**Available certificates to renew:**
- admin.conf
- apiserver
- apiserver-etcd-client
- apiserver-kubelet-client
- controller-manager.conf
- etcd-healthcheck-client
- etcd-peer
- etcd-server
- front-proxy-client
- scheduler.conf

### Configuration Management

#### `kubeadm config`
Manages kubeadm configuration.
```bash
kubeadm config print init-defaults                    # Print init defaults
kubeadm config print join-defaults                    # Print join defaults
kubeadm config migrate                                # Migrate old config
kubeadm config images list                            # List required images
kubeadm config images pull                            # Pull required images
kubeadm config view                                   # View current config
```

### Cluster Reset

#### `kubeadm reset`
Reverts changes made by kubeadm init or join.
```bash
kubeadm reset                                         # Reset node
kubeadm reset --force                                 # Skip confirmation
kubeadm reset --cleanup-tmp-dir                       # Clean tmp directory
kubeadm reset --cert-dir=<path>                       # Custom cert directory
kubeadm reset --cri-socket=<socket>                   # Specify CRI socket
```

**What gets reset:**
- Stops kubelet
- Removes containers
- Unmounts volumes
- Removes files in /etc/kubernetes/
- Cleans iptables rules
- Removes IPVS rules

### Upgrade Management

#### `kubeadm upgrade`
Manages cluster upgrades.
```bash
kubeadm upgrade plan                                  # Check upgrade plan
kubeadm upgrade apply v1.25.0                         # Upgrade to version
kubeadm upgrade apply --force                         # Force upgrade
kubeadm upgrade apply --dry-run                       # Preview upgrade
kubeadm upgrade apply --etcd-upgrade=false            # Skip etcd upgrade
kubeadm upgrade node                                  # Upgrade worker node
kubeadm upgrade node --certificate-renewal=false      # Skip cert renewal
kubeadm upgrade diff v1.25.0                          # Show config differences
```

**Upgrade process:**
1. Run on first control plane: `kubeadm upgrade plan`
2. Apply upgrade: `kubeadm upgrade apply v1.x.x`
3. Upgrade kubelet and kubectl packages
4. For other control plane nodes: `kubeadm upgrade node`
5. For worker nodes: `kubeadm upgrade node`

### Phase Management

#### `kubeadm init phase`
Executes specific phases of cluster initialization.
```bash
kubeadm init phase preflight                          # Run preflight checks
kubeadm init phase certs all                          # Generate all certificates
kubeadm init phase certs apiserver                    # Generate API server cert
kubeadm init phase kubeconfig all                     # Generate all kubeconfigs
kubeadm init phase kubeconfig admin                   # Generate admin kubeconfig
kubeadm init phase control-plane all                  # Deploy all control plane components
kubeadm init phase etcd local                         # Deploy local etcd
kubeadm init phase upload-config kubeadm              # Upload kubeadm config
kubeadm init phase mark-control-plane                 # Mark node as control-plane
kubeadm init phase bootstrap-token                    # Generate bootstrap token
kubeadm init phase addon all                          # Install all addons
```

#### `kubeadm join phase`
Executes specific phases of node join.
```bash
kubeadm join phase preflight                          # Run preflight checks
kubeadm join phase control-plane-prepare              # Prepare control plane
kubeadm join phase control-plane-join                 # Join control plane
kubeadm join phase kubelet-start                      # Start kubelet
```

### Utility Commands

#### `kubeadm version`
Prints kubeadm version.
```bash
kubeadm version
kubeadm version --output=json                         # JSON output
kubeadm version --output=yaml                         # YAML output
```

#### `kubeadm alpha`
Experimental/alpha features (varies by version).
```bash
kubeadm alpha certs renew                             # Alpha cert renewal
kubeadm alpha kubeconfig user                         # Generate user kubeconfig
kubeadm alpha selfhosting pivot                       # Pivot to self-hosted
```

#### `kubeadm completion`
Generates shell completion scripts.
```bash
kubeadm completion bash                               # Bash completion
kubeadm completion zsh                                # Zsh completion
source <(kubeadm completion bash)                     # Load completion
```

---

## Common Workflow Examples

### Complete Cluster Setup with kubeadm
```bash
# On control plane node
kubeadm init --pod-network-cidr=10.244.0.0/16
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
kubectl apply -f <cni-plugin.yaml>

# On worker nodes
kubeadm join <endpoint>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

### Local Development with kind
```bash
# Create cluster
kind create cluster --name dev

# Load local image
docker build -t myapp:latest .
kind load docker-image myapp:latest --name dev

# Deploy
kubectl apply -f deployment.yaml

# Delete when done
kind delete cluster --name dev
```

### Debugging Workflow
```bash
# Check pod status
kubectl get pods
kubectl describe pod <pod-name>

# View logs
kubectl logs <pod-name> --follow

# Execute commands
kubectl exec -it <pod-name> -- /bin/bash

# Port forward for testing
kubectl port-forward <pod-name> 8080:80

# Check resource usage
kubectl top pods
kubectl top nodes
```

### Deployment Management
```bash
# Create deployment
kubectl create deployment nginx --image=nginx:latest

# Scale
kubectl scale deployment nginx --replicas=3

# Update image
kubectl set image deployment/nginx nginx=nginx:1.21

# Check rollout
kubectl rollout status deployment/nginx

# Rollback if needed
kubectl rollout undo deployment/nginx
```

---

## Best Practices

1. **Use declarative configuration** (kubectl apply) over imperative commands
2. **Always specify namespace** explicitly with `-n` flag
3. **Use labels** for resource organization and selection
4. **Version control** your YAML manifests
5. **Use dry-run** to preview changes: `--dry-run=client -o yaml`
6. **Monitor resource usage** regularly with `kubectl top`
7. **Keep kubeconfig secure** and use context switching
8. **Regular backups** of etcd and important configurations
9. **Test in kind** before deploying to production clusters
10. **Use RBAC** properly for access control
