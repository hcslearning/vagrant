# vagrant
Ejemplos de uso con vagrant

## Synced folders
En Fedora 30 es necesario ejecutar lo siguiente para que funcione. 

```
sudo dnf install nfs-utils && sudo systemctl enable nfs-server

sudo firewall-cmd --permanent --add-service=nfs \
    && sudo firewall-cmd --permanent --add-service=rpc-bind \
    && sudo firewall-cmd --permanent --add-service=mountd \
    && sudo firewall-cmd --reload
    
```

## Ejemplo de Vagrantfile

```
Vagrant.configure("2") do |config|
  # definicion de la imagen a utilizar
  config.vm.box = "centos/7"
  
  # configuracion de varios directorios compartidos con NFS 4
  config.vm.synced_folder "./", "/vagrant",
    type: "nfs",
    nfs_version: 4,
    nfs_udp: false  

  config.vm.synced_folder "./../", "/home/vagrant/uno", type: "nfs", nfs_version: 4, nfs_udp: false
  config.vm.synced_folder "./../../", "/home/vagrant/dos", type: "nfs", nfs_version: 4, nfs_udp: false

  # script de provision que se ejecuta al inicio
  config.vm.provision "shell", inline: <<-SHELL
yum install -y httpd
systemctl start httpd
echo "hola" > /var/www/html/index.html
SHELL

end
```
