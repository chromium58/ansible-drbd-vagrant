$instances = 2

Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/xenial64"
  config.ssh.forward_agent = true
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "512", "--uartmode1", "disconnected" ]
  end

  (1..$instances).each do |i|
    config.vm.define "machine#{i}" do |config|
      config.vm.hostname = "machine#{i}"
      file_for_disk = "./large_disk#{i}.vdi"
      config.vm.provider "virtualbox" do |v|
        unless File.exist?(file_for_disk)
           v.customize ['createhd', '--filename',file_for_disk,'--size', 8192]
           v.customize ['storageattach', :id,'--storagectl', 'SCSI','--port', 2, '--device', 0, '--type', 'hdd', '--medium', file_for_disk]
        end
      end
      ip = "192.168.33.#{10*i}"
      config.vm.network :private_network, ip: ip
      if i == $instances
        config.vm.provision :ansible do |ansible|
          ansible.extra_vars = 'vars.yml'
          ansible.limit = 'all'
          ansible.playbook = 'playbook.yml'
          ansible.vault_password_file = "~/.ansible/ceph.secret"
          ansible.groups = {
            'drbd-nodes' => ['machine1','machine2'],
            }
#          ansible.verbose = ENV['ANSIBLE_VERBOSE'] ||= "v"
          ansible.tags = ENV['ANSIBLE_TAGS'] ||= "all"
          ansible.raw_arguments = [
            '--become',
          ]
        end
      end
    end
  end
end
