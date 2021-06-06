IMAGE_NAME = "ubuntu/focal64"
K8S_NAME = "sjangra"
MASTERS_NUM = 1
MASTERS_CPU = 2
MASTERS_MEM = 4096

NODES_NUM = 1
NODES_CPU = 2
NODES_MEM = 4096

IP_BASE = "192.168.1."
K8S_POD_CIDR = "172.20.0.0/20"

VAGRANT_DISABLE_VBOXSYMLINKCREATE=1

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false
    # below line is only needed if you want to push your own public key to all vms so that you can ssh into vm
    config.vm.provision "file", source: "d:/vagrant/sjangra.pub", destination: "~/.ssh/authorized_keys"

    (1..MASTERS_NUM).each do |i|
        config.vm.define "k8s-m-#{i}" do |master|
            master.vm.box = IMAGE_NAME
            master.vm.network "public_network", adapter: "1", ip: "#{IP_BASE}#{i + 150}"
            master.vm.hostname = "k8s-m-#{i}"
            master.vm.provider "virtualbox" do |v|
                v.name = "k8s-m-#{i}"
                v.memory = MASTERS_MEM
                v.cpus = MASTERS_CPU
            end
            master.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "roles/k8s.yml"
                #Redefine defaults
                ansible.extra_vars = {
                    k8s_cluster_name: K8S_NAME,
                    k8s_master_admin_user:  "vagrant",
                    k8s_master_admin_group: "vagrant",
                    k8s_master_apiserver_advertise_address: "#{IP_BASE}#{i + 150}",
                    k8s_master_node_name: "k8s-m-#{i}",
                    k8s_node_public_ip: "#{IP_BASE}#{i + 10}",
                    k8s_master_pod_network_cidr: K8S_POD_CIDR
                }
            end
        end
    end

    (1..NODES_NUM).each do |j|
        config.vm.define "k8s-n-#{j}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "public_network", adapter: "1", ip: "#{IP_BASE}#{j + 160}"
            node.vm.hostname = "k8s-n-#{j}"
            node.vm.provider "virtualbox" do |v|
                v.name = "k8s-n-#{j}"
                v.memory = NODES_MEM
                v.cpus = NODES_CPU
                #v.customize ["modifyvm", :id, "--cpuexecutioncap", "20"]
            end
            node.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "roles/k8s.yml"
                #Redefine defaults
                ansible.extra_vars = {
                    k8s_cluster_name: K8S_NAME,
                    k8s_node_admin_user:  "vagrant",
                    k8s_node_admin_group: "vagrant",
                    k8s_node_public_ip: "#{IP_BASE}#{j + 160}"
                }
            end
        end
    end
end
