Kubernetes manifests for Nextcloud All-In-One (AIO)

Files:
- k8s/deployment.yaml: Deployment for Nextcloud AIO (mounts PVCs and hostPath docker.sock)
- k8s/service.yaml: ClusterIP Service exposing ports 80, 8080, 8443
- k8s/pvc.yaml: Two PVCs: `aio-config-pvc` (5Gi) and `nextcloud-data-pvc` (10Gi)
- k8s/ingress.yaml: Ingress rule for host `nextcloud.nikitades.com` (no TLS configured)

Apply (namespace-aware order):

```bash
# create namespace first
kubectl apply -f k8s/namespace.yaml

# create secret and PVs (they reference the `nextcloud` namespace)
kubectl apply -f k8s/storagebox-secret.yaml
kubectl apply -f k8s/pvs.yaml

# create PVCs and remaining resources
kubectl apply -f k8s/pvc.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
```

Notes:
- The manifest mounts the host's `/var/run/docker.sock` as a `hostPath` socket (read-only). This mirrors the Docker-run behavior but requires cluster node access and may be a security concern.
- Adjust storage sizes, `storageClassName`, and `replicas` to match your environment.
- Ingress assumes an Ingress controller is present. TLS/certificates are omitted per request.

Hetzner Storage Box (optional):

- A sample Secret and two PVs for a Hetzner Storage Box are included:
	- `k8s/storagebox-secret.yaml` (contains placeholders; update `username` and `password`)
	- `k8s/pvs.yaml` (two PVs pre-bound to `aio-config-pvc` and `nextcloud-data-pvc` via `claimRef`)

Apply the secret and PVs before the PVCs if you want them to bind immediately:

```bash
kubectl apply -f k8s/storagebox-secret.yaml
kubectl apply -f k8s/pvs.yaml
kubectl apply -f k8s/pvc.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/ingress.yaml
```

Notes about SMB CSI:

- The PVs use the `smb.csi.k8s.io` CSI driver. Ensure an SMB CSI driver is installed in your cluster (for example https://github.com/kubernetes-csi/csi-driver-smb) — otherwise the PVs will not be usable.
- Update `k8s/storagebox-secret.yaml` with the real Storage Box credentials before applying.
- The `volumeAttributes.source` values point to `//u527831.your-storagebox.de/<share>`; adjust share names if needed.
