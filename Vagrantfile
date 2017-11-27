# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
# # NFS Server
  config.vm.define "nfs_srv" do |nfs_srv|
    nfs_srv.vm.box = "centos/7"
    nfs_srv.vm.box_check_update = false
    # Create a private network
    nfs_srv.vm.network "private_network", ip: "192.168.51.5", virtualbox__intnet: "sw0"
    nfs_srv.vm.hostname = "srv"
    # Provider
        nfs_srv.vm.provider "virtualbox" do |vb|
        # VM Conf
        vb.name = "nfs_srv"
        vb.cpus = 1
        vb.memory = "512"
        end

    # Enable provisioning with a shell script.
    nfs_srv.vm.provision "shell", inline: <<-SHELL
    yum install nfs-utils -y
    for action in {start,enable}; do
      echo 'performing action $action'
      systemctl $action nfs-server.service
      systemctl $action firewalld.service
    done
    mkdir -p /var/nfsfileshare
    chown -R nfsnobody:nfsnobody /var/nfsfileshare
    echo "/var/nfsfileshare 192.168.51.0/24(rw,sync,all_squash,no_subtree_check)" > /etc/exports
    exportfs -a
    firewall-cmd --permanent --zone public --add-service nfs
    firewall-cmd --reload
# rpcdebug -m rpc -s all    # sets all debug flags for RPC
# rpcdebug -m rpc -c all    # clears all debug flags for RPC
# rpcdebug -m nfsd -s all   # sets all debug flags for NFS Server
# rpcdebug -m nfsd -c all   # clears all debug flags for NFS Server
       SHELL

  end

# # NFS Client-1
  config.vm.define "nfs_cl1" do |nfs_cl1|
    nfs_cl1.vm.box = "centos/7"
    nfs_cl1.vm.box_check_update = false
    # Create a private network
    nfs_cl1.vm.network "private_network", ip: "192.168.51.10", virtualbox__intnet: "sw0"
    nfs_cl1.vm.hostname = "cl1"
    # Provider
        nfs_cl1.vm.provider "virtualbox" do |vb|
        # VM Conf
        vb.name = "nfs_cl1"
        vb.cpus = 1
        vb.memory = "512"
        end

    # Enable provisioning with a shell script.
    nfs_cl1.vm.provision "shell", inline: <<-SHELL
    yum install nfs-utils -y
    for action in {start,enable}; do
      echo 'performing action $action'
      systemctl $action firewalld.service
    done
    mkdir -p /mnt/nfsfileshare
    grep '192.168.51.5:/var/nfsfileshare' /etc/fstab >/dev/null
    if [ $? -ne 0 ]; then
      echo 'mounting nfsfileshare'
      echo "192.168.51.5:/var/nfsfileshare/ /mnt/nfsfileshare nfs rw,sync,hard,intr 0 0" >> /etc/fstab
      mount -a
     else
      echo 'share is already mounted'
    fi
    echo 'Can I write?' > /mnt/nfsfileshare/test
    if [ $? -ne 0 ]; then 
      echo 'I cant write!!!'
      exit 1
    fi
    echo 'The share seems to be working'
       SHELL

  end

# # NFS Client-2
  config.vm.define "nfs_cl2" do |nfs_cl2|
    nfs_cl2.vm.box = "centos/7"
    nfs_cl2.vm.box_check_update = false
    # Create a private network
    nfs_cl2.vm.network "private_network", ip: "192.168.51.20", virtualbox__intnet: "sw0"
    nfs_cl2.vm.hostname = "cl2"
    # Provider
        nfs_cl2.vm.provider "virtualbox" do |vb|
        # VM Conf
        vb.name = "nfs_cl2"
        vb.cpus = 1
        vb.memory = "512"
        end

    # Enable provisioning with a shell script.
    nfs_cl1.vm.provision "shell", inline: <<-SHELL
    yum install nfs-utils -y
    for action in {start,enable}; do
      echo 'performing action $action'
      systemctl $action firewalld.service
    done
    mkdir -p /mnt/nfsfileshare
    grep '192.168.51.5:/var/nfsfileshare' /etc/fstab >/dev/null
    if [ $? -ne 0 ]; then
      echo 'mounting nfsfileshare'
      echo "192.168.51.5:/var/nfsfileshare/ /mnt/nfsfileshare nfs rw,sync,hard,intr 0 0" >> /etc/fstab
      mount -a
     else
      echo 'share is already mounted'
    fi
    echo 'Can I write?' > /mnt/nfsfileshare/test
    if [ $? -ne 0 ]; then 
      echo 'I cant write!!!'
      exit 1
    fi
    echo 'The share seems to be working'
       SHELL

  end

end
