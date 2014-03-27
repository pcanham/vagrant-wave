# -*- mode: ruby -*-
# vi: set ft=ruby :

$script = <<SCRIPT
echo "*               soft    nofile          2048" >> /etc/security/limits.conf
yum install -y git java-1.6.0-openjdk java-1.6.0-openjdk-devel
cd /opt
wget http://archive.apache.org/dist/ant/binaries/apache-ant-1.9.3-bin.tar.gz
tar xvfz apache-ant-1.9.3-bin.tar.gz
ln -s /opt/apache-ant-1.9.3 /opt/ant
useradd wave
cd /home/wave
sudo -u wave -H git clone https://git-wip-us.apache.org/repos/asf/incubator-wave.git /home/wave/wave
cd /home/wave/wave
sudo -u wave -H git checkout wave-0.4-release
sed -i 's/-localWorkers 4/-localWorkers 1/g' build.xml
sudo -u wave -H /opt/ant/bin/ant get-third-party
sudo -u wave -H /opt/ant/bin/ant compile-gwt dist-server
sudo -u wave -H /opt/ant/bin/ant -f server-config.xml -Dwave_server_domain=sandbox.internal -Dhttp_frontend_public_address=10.0.0.20:8282
SCRIPT


Vagrant.configure("2") do |config|

  config.vm.provider :virtualbox do |vb|
    vb.gui = true
    vb.customize [
      "modifyvm", :id,
      "--memory", "512",
      "--cpus", "4",
      "--natdnspassdomain1", "off",
      ]
  end


  config.vm.define :waveinabox do |waveinabox|
    waveinabox.vm.box = "puppet-centos-65-x64"
    waveinabox.vm.box_url = "http://puppet-vagrant-boxes.puppetlabs.com/centos-65-x64-virtualbox-puppet.box"
    waveinabox.vm.network "private_network", ip: "10.0.0.20"
    #waveinabox.vm.network "public_network"
    waveinabox.vm.hostname = "wave"
    waveinabox.vm.provider :virtualbox do |v|
      v.customize ["modifyvm", :id, "--memory", "2048" ]
    end
    waveinabox.vm.provision :shell, :inline => $script
  end
end
