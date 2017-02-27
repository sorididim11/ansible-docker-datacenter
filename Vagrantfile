
Vagrant.configure('2') do |config|
  config.vm.box = 'centos/7'

  config.vm.provider 'virtualbox' do |vb|
    vb.memory = '1024'
  end

  config.vm.define 'mgmt' do |mgmt|
    mgmt.vm.hostname = 'mgmt'
    mgmt.vm.network :private_network, ip: '192.168.77.20'
    mgmt.vm.provision :ansible_local do |ansible|
      ansible.playbook = 'playbooks/site.yml'
      ansible.inventory_path = 'playbooks/stage'
      ansible.verbose = true
    end
  end

  N = 1

  (1..N).each do |machine_id|
    hostname = "kin-#{machine_id}"
    config.vm.define hostname do |node|
      node.vm.hostname = hostname
      node.vm.network :private_network, ip: "192.168.77.#{20 + machine_id}"
    end
  end
end
