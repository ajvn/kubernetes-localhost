ENV["LC_ALL"] = "en_US.UTF-8"
IMAGE_NAME = "ubuntu/bionic64"
N = 2

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.box_check_update = "True"
        master.vm.network "private_network", ip: "192.168.100.100"
        master.vm.hostname = "k8s-master"
        master.vm.provider "virtualbox" do |master|
          master.memory = "2048"
          master.cpus = 2
        end
        master.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "ansible/playbooks/kubernetes/kubernetes-master-install.yaml"
            ansible.extra_vars = {
                node_ip: "192.168.100.100",
            }
        end
        # It is also possible to specify this playbook to run as part of
        # kubernetes-master-install.yaml, if you prefer it that way.
        master.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "ansible/playbooks/lb/lb-deployment.yaml"
        end
        master.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "ansible/playbooks/dashboard/kubernetes-dashboard-install.yaml"
        end

    end

    (1..N).each do |node_id|
        config.vm.define "k8s-node-#{node_id}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.box_check_update = "True"
            node.vm.network "private_network", ip: "192.168.100.10#{node_id}"
            node.vm.hostname = "k8s-node-#{node_id}"
            node.vm.provider "virtualbox" do |node|
              node.memory = "2048"
              node.cpus = 2
            end
            node.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "ansible/playbooks/kubernetes/kubernetes-node-install.yaml"
                ansible.extra_vars = {
                    node_ip: "192.168.100.10#{node_id}",
                }
            end
        end
    end

    #TODO: Create NFS server for persistent storage
    #config.vm.define "k8s-nfs-server" do |nfs|
    #    nfs.vm.box = IMAGE_NAME
    #    nfs.vm.box_check_update = "True"
    #    nfs.vm.network "private_network", ip: "192.168.90.1"
    #    nfs.vm.hostname = "k8s-nfs-server"
    #    nfs.vm.provider "virtualbox" do |nfs|
    #      nfs.memory = "256"
    #      nfs.cpus = 1
    #    end
    #    nfs.vm.provision "ansible" do |ansible|
    #        ansible.playbook = "ansible/playbooks/nfs/nfs_server.yml"
    #        ansible.extra_vars = {
    #            node_ip: "192.168.50.200",
    #        }
    #    end
    #end
end
