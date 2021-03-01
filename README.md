## Выполнено ДЗ №6

## В процессе сделано:
  - Скачать и распаковать исходники nginx rpm

  ````
  wget https://nginx.org/packages/centos/7/SRPMS/nginx-1.14.1-1.el7_4.ngx.src.rpm
  rpm -i nginx-1.14.1-1.el7_4.ngx.src.rpm
  ````

  - Установка зависимостей:

  ````
  yum-builddep /root/rpmbuild/SPECS/nginx.spec -y
wget https://www.openssl.org/source/latest.tar.gz
tar -xvf latest.tar.gz --directory /usr/lib
  ````

  - Добавим openssl в конфигурацию nginx

  ````
sed -i 's|--with-debug|--with-openssl=/usr/lib/openssl-1.1.1j|' /root/rpmbuild/SPECS/nginx.spec
  ````

  - Соберем новый пакет nginx 

  ````
  rpmbuild --bb /root/rpmbuild/SPECS/nginx.spec
  ````

  - установим и настроим nginx из только что созданного пакета

  ````
  yum localinstall -y /root/rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm
sed -i '/index  index.html index.htm;/a autoindex on;' /etc/nginx/conf.d/default.conf
systemctl enable --now nginx

  ````

  - Создадим репозиторий

  ````
 mkdir /usr/share/nginx/html/repo
cp /root/rpmbuild/RPMS/x86_64/nginx-1.14.1-1.el7_4.ngx.x86_64.rpm /usr/share/nginx/html/repo/
createrepo /usr/share/nginx/html/repo/

  ````

  - Добавим репозиторий в список доступных

  ````
 cat >> /etc/yum.repos.d/custom.repo << EOF
[custom]
name=otus-lab6
baseurl=http://localhost/repo
gpgcheck=0
enabled=1
EOF

  ````

 Как проверить

  ````
 vagrant up
 vagrant ssh
 yum provides nginx
 yum repolist

  ````
