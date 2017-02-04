Vagrant.configure("2") do |config|

  # create mgmt node
  config.vm.define :mgmt do |mgmt_config|
      mgmt_config.vm.box = "bento/centos-7.2"
      mgmt_config.vm.hostname = "mgmt"
      mgmt_config.vm.network :private_network, ip: "10.0.15.10"
      
      mgmt_config.vm.provision :shell, path: "bootstrap-mgmt.sh"
      # mgmt_config.vm.provision :ansible_local do |ansible|
      #   ansible.playbook = ""
      # end
      #mgmt_config.vm.synced_folder 'ansible-examples', '/home/vagrant'

      mgmt_config.vm.provider "virtualbox" do |vb|
        vb.memory = "1024"
      end
      #mgmt_config.vm.provision :shell, path: "bootstrap.sh"
  end

  (1..3).each do |i|
    config.vm.define "kin-kube-#{i}" do |node|
        node.vm.box = "bento/centos-7.2"
        node.vm.hostname = "kin-kube-#{i}"
        node.vm.network :private_network, ip: "10.0.15.2#{i}"

        node.vm.provider "virtualbox" do |vb|
          vb.memory = "256"
        end
    end
  end

end