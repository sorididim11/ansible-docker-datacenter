
Vagrant.configure('2') do |config|
  config.vm.box = 'centos/7'

  # config.ssh.insert_key = false
  # config.ssh.private_key_path = '~/.vagrant.d/insecure_private_key'
  # ssh_pub_key = File.readlines('./key/id_rsa.pub').first.strip
  # ssh_pri_key = File.readlines('./key/id_rsa')

  config.vm.provider 'virtualbox' do |vb|
    vb.memory = '1024'
  end

  if Vagrant.has_plugin?('vagrant-cachier')
    config.cache.scope = :box
  end

  if Vagrant.has_plugin?('vagrant-proxyconf')
    config.proxy.http = 'http://web-proxy.corp.hp.com:8080'
    config.proxy.https = 'http://web-proxy.corp.hp.com:8080'
    config.proxy.no_proxy = 'localhost, 127.0.0.1'
  end

  N = 2

  (1..N).each do |machine_id|
    hostname = "kin-#{machine_id}"
    config.vm.define hostname do |node|
      node.vm.hostname = hostname
      node.vm.network :private_network, ip: "192.168.77.#{20 + machine_id}"
      # node.vm.provision :shell, inline: "echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys"
    end
  end

  config.vm.define 'mgmt' do |mgmt|
    mgmt.vm.hostname = 'mgmt'
    mgmt.vm.network :private_network, ip: '192.168.77.20'
    mgmt.vm.network 'forwarded_port', guest: 443, host: 8000
    # mgmt.vm.provision :shell, inline: "echo #{ssh_pri_key} > /home/vagrant/.ssh/id_rsa"
    mgmt.vm.provision :ansible_local do |ansible|
      ansible.install_mode = :pip
      ansible.version = '2.1.0'
      ansible.playbook = 'playbooks/site.yml'
      ansible.inventory_path = 'playbooks/dev'
      ansible.limit = 'all'
      ansible.verbose = 'true'
    end
  end


end
