# Vagrant box

Ubuntu server 22.04 based box that I use to setup a local Kubernetes cluster with [RKE2](https://docs.rke2.io/install/quickstart) and VMware fusion on a arm64 Mac m1 arch.

Provider: [vmware_desktop](https://developer.hashicorp.com/vagrant/docs/providers/vmware)

> vmdk files are not uploaded atm

## Some commands

### Vagrant

```bash
# Construct box
tar czvf ubuntu-server-22.04-arm64.box *

# Add custom box
vagrant box add --name ubuntu-server-22.04-arm64 ubuntu-server-22.04-arm64.box

# Create default Vagrantfile
vagrant init ubuntu-server-22.04-arm64
```

### k8s cluster

```bash
# === setup master ===
vagrant ssh master-1
sudo su

# pre-configure advertised ip
mkdir -p /etc/rancher/rke2
echo "node-ip: 172.16.72.150" > /etc/rancher/rke2/config.yaml

# install
curl -sfL https://get.rke2.io | sh -
systemctl enable rke2-server.service
systemctl start rke2-server.service
# view logs (optional)
journalctl -u rke2-server -f

# print token for the workers in the next steps
cat /var/lib/rancher/rke2/server/node-token

# === configure kubeconfig on the host ===
chmod go+r /etc/rancher/rke2/rke2.yaml
exit
exit
# on the host machine
scp vagrant@172.16.72.150:/etc/rancher/rke2/rke2.yaml kubeconfig
sed -i "" s/127.0.0.1/172.16.72.150/ kubeconfig
ln -f -s /Users/$USER/work/k8s-node-vagrant-box/kubeconfig /Users/$USER/.kube/config

# === setup workers ===
vagrant ssh worker-1
sudo su
curl -sfL https://get.rke2.io | INSTALL_RKE2_TYPE="agent" sh -
systemctl enable rke2-agent.service

# config worker to join master
export NODE_TOKEN=<node-token>
mkdir -p /etc/rancher/rke2/
cat << EOF > /etc/rancher/rke2/config.yaml
server: https://172.16.72.150:9345
token: $NODE_TOKEN
EOF
systemctl start rke2-agent.service
# repeat with worker-n
```

## HA cluster

```
// todo
```

## Links

[Vagrant SSH keys](https://github.com/hashicorp/vagrant/tree/main/keys)

[Creating a base box](https://developer.hashicorp.com/vagrant/docs/boxes/base)

[Vmware box format](https://developer.hashicorp.com/vagrant/docs/providers/vmware/boxes#ethernet-pcislotnumber)

[RKE2](https://docs.rke2.io/install/quickstart)