# _RPM   
Vagrantfile собирает машину с репозитарием `http://localhost/repo/` и кастомным `nginx-1.14.1-1.e17_4` c `openssl-1.1.1d`,   
помещенным в репозитарий.   
* установка nginx для создания репо   
`yum install nginx -y -q`   
`yum install createrepo -y -q`
* установка пакетов и зависимостей для сборки RPM из исходника.   
`yum install rpmdevtools rpm-build -y -q`   
`yum install tree yum-utils mc wget gcc vim git -y -q redhat-lsb-core`    
`yum install mock -y -q`    
* добавляем в группу    
`usermod -a -G mock root`  
* скачиваем `openssl`, распаковываем и копируем по пути `/root/`    
`sudo wget https://www.openssl.org/source/latest.tar.gz`    
`sudo tar -xvf latest.tar.gz`   
`cp -r openssl-1.1.1d/ /root/`    
* скачиваем SRPM nginx и подготавливаем к сборке    
`sudo wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.14.1-1.el7_4.ngx.src.rpm`    
`sudo rpm -ihv nginx-1.14.1-1.el7_4.ngx.src.rpm`
* копируем заранее подготовленные конфиги nginx,репо и SPEC   
`cp /vagrant/nginx.spec /root/rpmbuild/SPECS/`    
nginx.spec имеет параметр  `--with-openssl=/root/openssl-1.1.1d`    
`cp /vagrant/default.conf /etc/nginx/conf.d/`   
`cp /vagrant/otus.repo /etc/yum.repos.d/`   
* собираем пакет `rpmbuild -bb`   
`sudo rpmbuild -bb /root/rpmbuild/SPECS/nginx.spec`   
* создаем директорию для репозитария и копируем собранный rpm nginx    
`mkdir /usr/share/nginx/html/repo`    
`cp /root/rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm /usr/share/nginx/html/repo/`   
`wget http://www.percona.com/downloads/percona-release/redhat/0.1-6/percona-release-0.1-6.noarch.rpm -O /usr/share/nginx/html/repo/percona-release-0.1-6.noarch.rpm`    

* создаем репозитирий     
`createrepo /usr/share/nginx/html/repo/`    

* перезапускаем nginx так как сменили default.conf ранее    
`sudo systemctl restart nginx`

