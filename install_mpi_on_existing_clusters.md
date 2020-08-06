# Install MPI in existing cluster

source: https://www.slothparadise.com/how-to-setup-mpi-on-centos-7-2-connected-virtual-machines-part-2/

# On Mac
```shell script
# Master node is ip-10-0-39-100.us-west-2.compute.internal

scp /Users/acharys/.ssh/lattice-dev.pem ip-10-0-39-100.us-west-2.compute.internal:/home/hadoop/.ssh/
```

# Setting up nodes for pcluster
For the existing cluster, we pick work on data nodes since they are ```m5.24xlarge``` EC2.
We would pick one of the data/core node of the existing cluster and call it as **pcluster-master-node**, rest of the data/core nodes we will call **pcluster-compute-nodes**. For example from ```original data nodes : 10.0.57.226  and  10.0.51.224```, we would just pick one as pcluster-master-node (say 10.0.57.226) and rest will be pcluster-compute-nodes
## On pcluster-master-node (say ip-10-0-57-226.us-west-2.compute.internal)
```shell script
# vim ~/.ssh/config
# original data/core nodes : 10.0.57.226  and  10.0.51.224
Host 10.0.57.226
    Hostname 10.0.57.226
    IdentityFile ~/.ssh/lattice-dev.pem
    ServerAliveInterval 30
    User hadoop

Host 10.0.51.224
    Hostname 10.0.51.224
    IdentityFile ~/.ssh/lattice-dev.pem
    ServerAliveInterval 30
    User hadoop
```
Change the permission of the file ```chmod 0644 ~/.ssh/config``` .

Now copy the pem file and config to reast of the pcluster-compute-nodes
```shell script
scp ~/.ssh/lattice-dev.pem 10.0.51.224:/home/hadoop/.ssh/

scp ~/.ssh/config 10.0.51.224:/home/hadoop/.ssh/
```

## Setup shared directory
On pcluster-master-node
```
rm    -r    /mnt/nfs
mkdir -p    /mnt/nfs

ln    -s    /mnt/nfs   ~/slink
```

On pcluster-compute-nodes
```
cd /tmp
wget https://rpmfind.net/linux/epel/6/x86_64/Packages/f/fuse-sshfs-2.4-1.el6.x86_64.rpm
sudo rpm -ivh fuse-sshfs-2.4-1.el6.x86_64.rpm

```
# Other things that were tried but failed
<details>
  <summary>Click to expand !!</summary>
```
# https://serverfault.com/questions/312472/what-does-that-mean-packages-excluded-due-to-repository-priority-protections

# https://github.com/libfuse/libfuse
cd /tmp
wget https://github.com/libfuse/libfuse/archive/fuse-3.9.2.tar.gz
tar xvf fuse-3.9.2.tar.gz
cd libfuse-fuse-3.9.2
mkdir build ; cd build
meson ..
ninja
sudo /usr/local/bin/ninja install




# https://www.howtoforge.com/tutorial/how-to-install-sshfs-on-centos-7/
# https://jamesnbr.wordpress.com/2019/10/24/install-sshfs-3-5-2-on-centos-8-rhel-8/
cd /tmp
wget https://github.com/libfuse/sshfs/archive/sshfs-3.7.0.tar.gz
tar xvf sshfs-3.7.0.tar.gz
cd sshfs-sshfs-3.7.0/
sudo pip-3.6 install meson ninja
# sudo yum install fuse3 fuse3-devel -y
mkdir build ; cd build


sudo yum upgrade
sudo yum install epel-release
sudo yum makecache fast

sudo yum install sshfs &&
mkdir -p /scratch/home/ec2-user &&
sshfs $MASTER_NODE:/scratch/home/ec2-user /scratch/home/ec2-user && df -h
```
</details>

