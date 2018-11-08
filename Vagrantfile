domain = 'techysoul.com'
# use two digits id below, please
nodes = [
  { :hostname => 'master1',  :ip => '192.168.33.10', :id => '10' },
  { :hostname => 'master2',  :ip => '192.168.33.11', :id => '11' },
  { :hostname => 'master3',  :ip => '192.168.33.12', :id => '12' },
  { :hostname => 'worker1',   :ip => '192.168.33.13', :id => '13' },
  { :hostname => 'worker2',   :ip => '192.168.33.14', :id => '14' },
  { :hostname => 'worker3',   :ip => '192.168.33.15', :id => '15' },
]
memory = 768


$script = <<SCRIPT
sudo mv hosts /etc/hosts
chmod 0600 /home/vagrant/.ssh/id_rsa
SCRIPT

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
      nodeconfig.vm.box = "rhel-7.4-201811-vagrant-v2"
      nodeconfig.vm.hostname = node[:hostname]
      nodeconfig.landrush.enabled = true
      nodeconfig.vm.network :private_network, ip: node[:ip]
      nodeconfig.vm.synced_folder "disk1", "/opt/disk1"
      nodeconfig.vm.provider :virtualbox do |vb|
        vb.name = node[:hostname]+"."+domain
        #vb.memory = memory
        #vb.cpus = 2
        vb.customize ['modifyvm', :id, '--natdnshostresolver2', 'on']
        vb.customize ['modifyvm', :id, '--natdnsproxy2', 'on']
        vb.customize ['modifyvm', :id, '--macaddress2', "5CA2AB2E00"+node[:id]]
        vb.customize ['modifyvm', :id, '--natnet2', "192.168/16"]
      end
      nodeconfig.vm.provision "file", source: "hosts", destination: "hosts"
      nodeconfig.vm.provision "file", source: "~/.vagrant.d/insecure_private_key", destination: "/home/vagrant/.ssh/id_rsa"
    end
  end
end
