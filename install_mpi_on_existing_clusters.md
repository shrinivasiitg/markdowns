# Install MPI in existing cluster

source: https://www.slothparadise.com/how-to-setup-mpi-on-centos-7-2-connected-virtual-machines-part-2/

## From mac
```shell script
# Master node is ip-10-0-39-100.us-west-2.compute.internal

scp /Users/acharys/.ssh/lattice-dev.pem ip-10-0-39-100.us-west-2.compute.internal:/home/hadoop/.ssh/
```

## From Master node (ip-10-0-39-100.us-west-2.compute.internal)
```shell script
# vim ~/.ssh/config
# master node: ip-10-0-39-100
# data nodes : 10.0.57.226  and  10.0.51.224
Host ip-10-0-39-100
    Hostname ip-10-0-39-100
    IdentityFile ~/.ssh/lattice-dev.pem
    ServerAliveInterval 30
    User hadoop

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

Now copy the pem file and config to each data node
```
scp ~/.ssh/lattice-dev.pem 10.0.57.226:/home/hadoop/.ssh/
scp ~/.ssh/lattice-dev.pem 10.0.51.224:/home/hadoop/.ssh/

scp ~/.ssh/config 10.0.57.226:/home/hadoop/.ssh/
scp ~/.ssh/config 10.0.51.224:/home/hadoop/.ssh/
```

