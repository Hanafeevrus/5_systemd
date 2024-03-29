# -*- mode: ruby -*-
# vim: set ft=ruby :
home = ENV['HOME']
ENV["LC_ALL"] = "en_US.UTF-8"

MACHINES = {
  :systemd => {
        :box_name => "centos/7",
        :box_version => "1804.02",
        :ip_addr => '192.168.11.101',
    :disks => {
        :sata1 => {
            :dfile => home + '/VirtualBox VMs/sata1.vdi',
            :size => 10240,
            :port => 1
        },

}
  },
}

Vagrant.configure("2") do |config|

    config.vm.box_version = "1804.02"
    MACHINES.each do |boxname, boxconfig|
  
        config.vm.define boxname do |box|
  
            box.vm.box = boxconfig[:box_name]
            box.vm.host_name = boxname.to_s
  
            #box.vm.network "forwarded_port", guest: 3260, host: 3260+offset
  
            box.vm.network "private_network", ip: boxconfig[:ip_addr]
  
            box.vm.provider :virtualbox do |vb|
                    vb.customize ["modifyvm", :id, "--memory", "256"]
                    needsController = false
            boxconfig[:disks].each do |dname, dconf|
                unless File.exist?(dconf[:dfile])
                  vb.customize ['createhd', '--filename', dconf[:dfile], '--variant', 'Fixed', '--size', dconf[:size]]
                                  needsController =  true
                            end
  
            end
                    if needsController == true
                       vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata" ]
                       boxconfig[:disks].each do |dname, dconf|
                           vb.customize ['storageattach', :id,  '--storagectl', 'SATA', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
                       end
                    end
            end
  
        box.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh
            cp ~vagrant/.ssh/auth* ~root/.ssh
	    cp /vagrant/watchlog /etc/sysconfig/
	    cp /vagrant/watchlog.sh /opt/
	    cp /vagrant/watchlog.timer /etc/systemd/system/
	    cp /vagrant/watchlog.service /etc/systemd/system/
	    cp /vagrant/watchlog.log /var/log/
	    sudo chmod +x /opt/watchlog.sh
	    sudo systemctl start watchlog.timer
	    sudo systemctl start watchlog.service
	    sudo yum install epel-release -y && yum install spawn-fcgi php php-cli mod_fcgid httpd -y
	    cp /vagrant/spawn-fcgi /etc/sysconfig/
	    cp /vagrant/spawn-fcgi.service /etc/systemd/system/
	    sudo systemctl start spawn-fcgi
	    cp /vagrant/config /etc/selinux/
	    cp /vagrant/first.conf /etc/httpd/conf/
	    cp /vagrant/second.conf /etc/httpd/conf/
	    cp /vagrant/httpd-first /etc/sysconfig/
	    cp /vagrant/httpd-second /etc/sysconfig/
	    cp /vagrant/httpd@.service /etc/systemd/system/
	    sudo systemctl daemon-reload
	    sudo systemctl enable httpd@first
            sudo systemctl enable httpd@second
	    sudo systemctl enable watchlog.timer
	    sudo systemctl enable watchlog.service
	    sudo shutdown -r now
	SHELL
  
        end
    end
  end
  
