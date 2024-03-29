# Bootstraping Manual

## Cluster init
You need to configure containerd to enable correct device ownership to use block volumes. Set this value in /etc/containerd/config.toml :
```toml
[plugins]
  [plugins."io.containerd.grpc.v1.cri"]
    device_ownership_from_security_context = true
```

Now, configure the control plane:
```bash
# kubeadm config file path = ./kubernetes/config.yaml
# You need to copy the file on the remote control-plane
kubeadm init --config FILE
```

## CNI
1. Install Kube-OVN
```bash
# Need to be root
./kube-ovn/install.sh
```

2. Setup cilium cni-configuration
```bash
# On all nodes
cat <<EOF > /etc/cni/net.d/05-cilium.conflist
{
  "name": "generic-veth",
  "cniVersion": "0.3.1",
  "plugins": [
    {
      "type": "kube-ovn",
      "server_socket": "/run/openvswitch/kube-ovn-daemon.sock",
      "ipam": {
          "type": "kube-ovn",
          "server_socket": "/run/openvswitch/kube-ovn-daemon.sock"
      }
    },
    {
      "type": "portmap",
      "snat": true,
      "capabilities": {"portMappings": true}
    },
    {
      "type": "cilium-cni"
    }
  ]
}
EOF
```

3. Deploy cilium
```bash
helm repo add cilium https://helm.cilium.io/
helm install cilium cilium/cilium --version 1.13.0 \
    --namespace kube-system \
    --set cni.chainingMode=generic-veth \
    --set cni.customConf=true \
    --set cni.readCniConf=/host/etc/cni/net.d/05-cilium.conflist \
    --set tunnel=disabled \
    --set enableIPv4Masquerade=false \
    --set enableIdentityMark=false
```

4. Remove control-plane taint

5. Deploy multus
```bash
kubectl apply -f https://raw.githubusercontent.com/k8snetworkplumbingwg/multus-cni/master/deployments/multus-daemonset.yml
```

## CSI
```bash
kubectl kustomize longhorn | kubectl apply -f -
```

## Flucd (GitOps)
Deploy fluxcd and gitops resources in the cluster
```bash
kubectl apply -f fluxcd/gotk-components.yaml
kubectl apply -f fluxcd/gotk-sync.yaml
kubectl apply -f fluxcd/apps-sync.yaml
```

## Internal Domain
```bash
kubectl apply -f powerdns/coredns-config.yaml
```

