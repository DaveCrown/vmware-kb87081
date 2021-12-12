
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "install.yml"
  end
end
