# -*- mode: ruby -*-
# vim: set ft=ruby :
# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :centos => {
        :box_name => "centos/7",
        :ip_addr => '192.168.11.101'
  }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s

          #box.vm.network "forwarded_port", guest: 3260, host: 3260+offset

          box.vm.network "private_network", ip: boxconfig[:ip_addr]

          box.vm.provider :virtualbox do |vb|
            vb.customize ["modifyvm", :id, "--memory", "256"]
          config.vm.synced_folder ".", "/vagrant", disabled: false
	  end

          box.vm.provision "shell", inline: <<-SHELL
#          mkdir -p ~root/.ssh
#          cp ~vagrant/.ssh/auth* ~root/.ssh
           yum install epel-release -y -q
           yum install fish wget -y -q
# Install tools for building rpm
           yum install rpmdevtools rpm-build -y -q
           yum install tree yum-utils mc wget gcc vim git -y -q redhat-lsb-core
# Install tools for building woth mock and make prepares    
           yum install mock -y -q
           usermod -a -G mock root
# Install tools for creating your own REPO
           yum install nginx -y -q
           yum install createrepo -y -q
# Install docker-ce
           #sudo yum install -y -q yum-utils links \
           #device-mapper-persistent-data \
           #lvm2
           #sudo yum-config-manager \
           #--add-repo \
           #https://download.docker.com/linux/centos/docker-ce.repo
           #yum install docker-ce docker-compose -y -q
           #systemctl start docker
           #docker run hello-world
# SPRM nginx	   
	   sudo wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.14.1-1.el7_4.ngx.src.rpm
	   sudo rpm -ihv nginx-1.14.1-1.el7_4.ngx.src.rpm
# wget OpenSSL
	   sudo wget https://www.openssl.org/source/latest.tar.gz
	   sudo tar -xvf latest.tar.gz
# install dependences
	   sudo yum-builddep -y nginx-1.14.1-1.el7_4.ngx.src.rpm
## copy openssl to /root
	   cp -r openssl-1.1.1d/ /root/
##copy nginx.spec default.conf otus.repo
	   cp /vagrant/nginx.spec /root/rpmbuild/SPECS/
	   cp /vagrant/default.conf /etc/nginx/conf.d/
	   cp /vagrant/otus.repo /etc/yum.repos.d/
#build rpm
	   sudo rpmbuild -bb /root/rpmbuild/SPECS/nginx.spec
#makedir repo
	   mkdir /usr/share/nginx/html/repo
### copy to repo .rpm
	   cp /root/rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm /usr/share/nginx/html/repo/
### copy percona-server
	   wget http://www.percona.com/downloads/percona-release/redhat/0.1-6/percona-release-0.1-6.noarch.rpm -O /usr/share/nginx/html/repo/percona-release-0.1-6.noarch.rpm
#create repo
	   createrepo /usr/share/nginx/html/repo/
#nginx reload
          # sudo nginx -s reload
	  sudo systemctl restart nginx
      SHELL

      end
  end
end
