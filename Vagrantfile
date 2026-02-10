Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/noble64"
  config.vm.network :private_network, ip: "192.168.56.89"
  config.vm.hostname = "server.test"
  config.ssh.insert_key = false

  config.vm.provider "virtualbox" do |v|
    if not File.exist?("zfsdisk.vdi")
      v.customize ["createhd",  "--filename", "zfsdisk.vdi", "--size", "512"]
    end
    v.customize ['storageattach', :id,  '--storagectl', 'SCSI', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', "zfsdisk.vdi"]
  end

  $script = "
    sudo usermod -u 2005 ubuntu
    sudo groupmod -g 2005 ubuntu
    sudo apt update && sudo apt -y upgrade && sudo apt -y install zfsutils-linux --no-install-recommends
    sudo zpool create data -m /mnt/data -o ashift=12 /dev/sdc
    "
  config.vm.provision "shell", inline: $script

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
    ansible.ask_vault_pass = true
  end
end
