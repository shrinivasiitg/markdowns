# Install MPI in existing cluster

source: https://www.slothparadise.com/how-to-setup-mpi-on-centos-7-2-connected-virtual-machines-part-2/

# From Mac
```shell script
# Master node is ip-10-0-39-100.us-west-2.compute.internal

scp /Users/acharys/.ssh/lattice-dev.pem ip-10-0-39-100.us-west-2.compute.internal:/home/hadoop/.ssh/
```

# Setting up nodes for pcluster
For the existing cluster, we pick work on data nodes since they are ```m5.24xlarge``` EC2.
We would pick one data node of the existing cluster and call it as **pcluster-master-node**, rest of the data nodes we will call **pcluster-master-nodes**. For example from ```original data nodes : 10.0.57.226  and  10.0.51.224```, we would just pick one as pcluster-master-node (say 10.0.57.226) and rest will be pcluster-data-nodes
## From pcluster-master-node (say ip-10-0-57-226.us-west-2.compute.internal)
```shell script
# vim ~/.ssh/config
# original data nodes : 10.0.57.226  and  10.0.51.224
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

Now copy the pem file and config to reast of the pcluster-master-nodes
```shell script
scp ~/.ssh/lattice-dev.pem 10.0.51.224:/home/hadoop/.ssh/

scp ~/.ssh/config 10.0.51.224:/home/hadoop/.ssh/
```

## Setup shared directory
From 

