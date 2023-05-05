# Install CNI
1. Install Kube-OVN
```bash
./install.sh
```

2. Setup cilium cni-configuration
```bash
# On all hosts
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

4. Deploy multus
```bash
kubectl apply -f https://raw.githubusercontent.com/k8snetworkplumbingwg/multus-cni/master/deployments/multus-daemonset.yml
```
