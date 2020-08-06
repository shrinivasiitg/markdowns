# Install MPI in existing cluster

source: https://www.slothparadise.com/how-to-setup-mpi-on-centos-7-2-connected-virtual-machines-part-2/

# On Mac
```shell script
# pcluster-master-node is ip-10-0-35-233.us-west-2.compute.internal

scp ~/.ssh/lattice-dev.pem      ip-10-0-35-233.us-west-2.compute.internal:/home/hadoop/.ssh/
scp ~/.ssh/t2-micro-032320.pem  ip-10-0-35-233.us-west-2.compute.internal:/home/hadoop/.ssh/
```

# Setting up nodes for pcluster
For the existing cluster, we pick work on data nodes since they are ```m5.24xlarge``` EC2.
We would pick one of the data/core node of the existing cluster and call it as **pcluster-master-node**, rest of the data/core nodes we will call **pcluster-compute-nodes**. For example from ```original data nodes : 10.0.35.233  and  10.0.42.97```, we would just pick one as pcluster-master-node (say 10.0.57.226) and rest will be pcluster-compute-nodes
## On pcluster-master-node (say ip-10-0-57-226.us-west-2.compute.internal)
```shell script
# vim ~/.ssh/config
# original data/core nodes : 10.0.57.226  and  10.0.51.224
Host 10.0.35.233
    Hostname 10.0.35.233
    IdentityFile ~/.ssh/lattice-dev.pem
    ServerAliveInterval 30
    User hadoop

Host 10.0.42.97
    Hostname 10.0.42.97
    IdentityFile ~/.ssh/lattice-dev.pem
    ServerAliveInterval 30
    User hadoop
    
Host ec2-ubuntu
    Hostname ec2-34-229-115-241.compute-1.amazonaws.com
    IdentityFile ~/.ssh/t2-micro-032320.pem
    user ubuntu
    ServerAliveInterval 300

Host lab
    Hostname localhost
    user aishwarya
    ServerAliveInterval 300
    Port 12345
    ProxyCommand ssh -W %h:%p ec2-ubuntu
```
Change the permission of the file ```chmod 0644 ~/.ssh/config``` .

Now copy the pem file and config to reast of the pcluster-compute-nodes
```shell script
scp ~/.ssh/lattice-dev.pem 10.0.51.224:/home/hadoop/.ssh/

scp ~/.ssh/config 10.0.51.224:/home/hadoop/.ssh/
```

## Setup shared directory
### On pcluster-master-node
```
rm    -r    /mnt/nfs
mkdir -p    /mnt/nfs
ln    -s    /mnt/nfs   ~/slink
sudo ln -s  /mnt/nfs    /nfs
```
Edit ~/.bashrc
```
setting sshfs
sshfs_lab='sshfs lab:/home/aishwarya/workspace  /nfs/sshfs-share-lab/home/aishwarya/workspace'
sshfs_lab_unmount='fusermount -u /nfs/sshfs-share-lab/home/aishwarya/workspace'
alias sshfs-lab='$sshfs_lab_unmount ; $sshfs_lab'
alias sshfs-lab-unmount='$sshfs_lab_unmount'
#########

#setting SHELL VARIABLE
SSHFS_PIPE=/nfs/sshfs-share-lab/home/aishwarya/workspace/PIPE/PIPE
LOCAL_PIPE=/nfs/workspace/PIPE/PIPE
#mkdir -p $LOCAL_PIPE
#chmod 777 -R $LOCAL_PIPE
cd $LOCAL_PIPE/
#########

export PS1="(\d  \t) <\`latest_exit_code\`> [\w]\n\[\e[36m\]\h:~ \u $\[\e[m\] "
function latest_exit_code() {
        echo "$?"
}
```
Mount sshfs-lab
```
mkdir -p $LOCAL_PIPE
mkdir -p /nfs/sshfs-share-lab/home/aishwarya/workspace
sshfs-lab
```
Now, upload ```~/.bashrc``` to each pcluster-compute-nodes
```
scp -i ~/.ssh/lattice-dev.pem ~/.bashrc 10.0.42.97:/home/hadoop/.bashrc
scp -i ~/.ssh/lattice-dev.pem ~/.bashrc 10.0.42.97:/home/hadoop/.bashrc
..
..
```

### each On pcluster-compute-nodes
```
# sudo yum install epel-release
# epel-release-6-8.9.amzn1.noarch
# hence install rpm for epel-6, which is https://rpmfind.net/linux/dag/redhat/el6/en/x86_64/dag/RPMS/fuse-sshfs-2.5-1.el6.rf.x86_64.rpm
# https://rpmfind.net/linux/rpm2html/search.php?query=fuse-sshfs


cd /tmp
wget https://rpmfind.net/linux/dag/redhat/el6/en/x86_64/dag/RPMS/fuse-sshfs-2.5-1.el6.rf.x86_64.rpm
sudo rpm -ivh fuse-sshfs-2.5-1.el6.rf.x86_64.rpm

mkdir -p /mnt/nfs
sshfs ip-10-0-35-233:/mnt/nfs /mnt/nfs -o IdentityFile=~/.ssh/lattice-dev.pem
# $MASTER_NODE=ip-10-0-35-233

sudo ln -s  /mnt/nfs    /nfs
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

## On all nodes
```
yum install gcc gcc-c++ gcc-fortran kernel-devel -y
```

```
cd /nfs/

vi hosts
# put ips of pcluster-master-node and all pcluster-compute-node
```


```
sudo yum install intltool
sudo yum install gperf
sudo yum install libcap-devel
```

Add ```~/.ssh/id_rsa.pub``` of each pcluster-compute-nodes to pcluster-compute-node and vice versa
