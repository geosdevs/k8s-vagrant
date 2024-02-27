Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu-server-22.04-arm64"

  config.vm.define "master-1" do |master|
    master.vm.network "private_network", ip: "172.16.72.150"
    master.vm.hostname = "master-1.local"
  end
  
  config.vm.define "worker-1" do |worker|
    worker.vm.network "private_network", ip: "172.16.72.151"
    worker.vm.hostname = "worker-1.local"
  end
  
  config.vm.define "worker-2" do |worker|
    worker.vm.network "private_network", ip: "172.16.72.152"
    worker.vm.hostname = "worker-2.local"
  end

  config.vm.provider "vmware_desktop" do |vmware|
    vmware.vmx["memsize"] = "2048"
    vmware.vmx["numvcpus"] = "4"
    vmware.allowlist_verified = true
    vmware.gui = false
  end
end
