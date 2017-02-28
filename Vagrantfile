
Vagrant.configure('2') do |config|
  config.vm.box = 'centos/7'
  config.ssh.insert_key = false
  
  config.vm.provider 'virtualbox' do |vb|
    vb.memory = '1024'
  end

  #ssh_pub_key = File.readlines('./key/id_rsa.pub').first.strip
  #ssh_pri_key = File.readlines('./key/id_rsa')

  N = 2

  (1..N).each do |machine_id|
    hostname = "kin-#{machine_id}"
    config.vm.define hostname do |node|
      node.vm.hostname = hostname
      node.vm.network :private_network, ip: "192.168.77.#{20 + machine_id}"
      #node.vm.provision :shell, inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys"
    end
  end

  config.vm.define 'mgmt' do |mgmt|
    mgmt.vm.box = 'ubuntu/trusty64'
    mgmt.vm.hostname = 'mgmt'
    mgmt.vm.network :private_network, ip: '192.168.77.20'
    #mgmt.vm.provision :shell, inline: "echo #{ssh_pri_key} > /home/vagrant/.ssh/id_rsa"
    mgmt.vm.provision :ansible_local do |ansible|
      ansible.playbook = 'playbooks/site.yml'
      ansible.inventory_path = 'playbooks/stage'
      ansible.limit = "all"
      #ansible.verbose = true
    end
  end


end
