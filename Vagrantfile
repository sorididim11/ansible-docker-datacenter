require 'yaml'
require 'vagrant/ui'

UI = Vagrant::UI::Colored.new

guest_home_dir = '/home/vagrant'
settings = YAML.load_file 'vagrantConf.yml'


# create dynamic inventory file. ansible provisioner 's dynamic inventory got some bugs
UI.info 'Create ansible dynamic inventory file...', bold: true
inventory_file = 'ansible/inventories/dev/hosts'
File.open(inventory_file, 'w') do |f|
  %w(docker_masters docker_slaves).each do |section|
    f.puts("[#{section}]")
    
    settings.each do |_, machine_info|
      f.puts(machine_info['ip']) if machine_info['type'] == section
    end
    f.puts('')
  end
  f.write("[docker_nodes:children]\ndocker_masters\ndocker_slaves")
end

Vagrant.configure('2') do |config|
  config.vm.box = 'centos/7'
  config.ssh.insert_key = false
  config.vm.synced_folder '.', '/vagrant', type: 'virtualbox'

  required_plugins = %w( vagrant-hostmanager vagrant-cachier vagrant-vbguest )
  required_plugins.each do |plugin|
    exec "vagrant plugin install #{plugin};vagrant #{ARGV.join(' ')}" unless Vagrant.has_plugin?(plugin) || ARGV[0] == 'plugin'
  end

  config.hostmanager.enabled = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.cache.scope = :machine
  config.vbguest.auto_update = true

  if Vagrant.has_plugin?('vagrant-proxyconf')
    config.proxy.http = 'http://web-proxy.kor.hp.com:8080'
    config.proxy.https = 'http://web-proxy.kor.hp.com:8080'

    no_proxy = 'localhost,127.0.0.1,' + settings.map { |_, v| "#{v['ip']},#{v['name']}" }.join(',')
    UI.info "no proxies: #{no_proxy}"
    config.proxy.no_proxy = no_proxy
  end


  settings.each do |name, machine_info|
    config.vm.define name do |node|
      node.vm.hostname = machine_info['name']
      node.vm.network :private_network, ip: machine_info['ip']
      !machine_info['box'].nil? && node.vm.box = machine_info['box']

      node.vm.provider 'virtualbox' do |vb|
        vb.linked_clone = true
        vb.cpus = machine_info['cpus']
        vb.memory = machine_info['mem']
        vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
        # for dcos ntptime
        vb.customize ['guestproperty', 'set', :id, '/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold', 1000]
      end


        node.vm.provision :ansible_local do |ansible|
          ansible.install_mode = :pip # or default( by os package manager)
          ansible.version = '2.4.3.0'
          ansible.config_file = 'ansible/ansible.cfg'
          ansible.inventory_path = inventory_file
          ansible.limit = 'all'

          # ansible.playbook = 'ansible/playbooks/util-config-ohmyzsh.yml'
          ansible.playbook = 'ansible/vagrantSite.yml'
          ansible.verbose = 'true'
          ansible.vault_password_file = guest_home_dir + '/password' if dcos_config['dcos_is_enterprise']
        end
      end
    end
  end
end
