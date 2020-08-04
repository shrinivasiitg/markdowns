# 1. on dev-dsk
```
# conda create --name pcluster_py3_7 python=3.7
conda activate pcluster_py3_7
# install aws-parallelcluster --upgrade --user
# on mac do this: 
## export PATH=~/.local/bin:$PATH
aws-lattice-dev
pcluster configure
pcluster list

Add these two IAM Policy to USER "arn:aws:iam::111111111111:user/lattice-emr"
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "ssm:GetParametersByPath",
            "Resource": "arn:aws:ssm:us-west-2:111111111111:parameter/aws/service/ami-amazon-linux-latest"
        }
    ]
}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "cloudformation:DescribeStacks",
            "Resource": "arn:aws:cloudformation:us-east-1:111111111111:stack/parallelcluster-acharys/*"
        }
    ]
}
cahnge the master sg to use the rang:
10.0.0.0/16
```
# 2. on-mac
## put pcluster-master name in ```~/.ssh/config```
```
scp ~/.ssh/*.pem pcluster-master:/home/ec2-user/.ssh/
scp ~/.ssh/config pcluster-master:/home/ec2-user/.ssh/

```

# 3. setup pcluster-master  
login to pcluster-master and find out the ```hostname``` (let's say it is ```ip-10-0-191-164```)
```
echo "" >> ~/.bashrc
echo "MASTER_NODE=ip-10-0-191-164" >> ~/.bashrc
```
## Setup slink
```
# On Master node
rm -rf /scratch/home/ec2-user/
mkdir -p /scratch/home/ec2-user/
ln -s /scratch/home/ec2-user ~/slink

# on each compute node (all 6)
sudo yum install sshfs && mkdir -p /scratch/home/ec2-user && sshfs $MASTER_NODE:/scratch/home/ec2-user /scratch/home/ec2-user && df -h
```
## setup sshfs-lab via ```~/.bashrc```
Paste following as-it-is in ```~/.bashrc```
```
#setting sshfs
sshfs_lab='sshfs lab:/home/aishwarya/workspace  /scratch/home/ec2-user/sshfs-share-lab/home/aishwarya/workspace'
sshfs_lab_unmount='fusermount -u /scratch/home/ec2-user/sshfs-share-lab/home/aishwarya/workspace'
alias sshfs-lab='$sshfs_lab_unmount ; $sshfs_lab'
alias sshfs-lab-unmount='$sshfs_lab_unmount'
#########

#setting SHELL VARIABLE
SSHFS_PIPE=/scratch/home/ec2-user/sshfs-share-lab/home/aishwarya/workspace/PIPE/PIPE
LOCAL_PIPE=/scratch/home/ec2-user/workspace/PIPE/PIPE
#mkdir -p $LOCAL_PIPE
#chmod 777 -R $LOCAL_PIPE
cd $LOCAL_PIPE/
#########

export PS1="(\d  \t) <\`latest_exit_code\`> [\w]\n\[\e[36m\]\h:~ \u $\[\e[m\] "
function latest_exit_code() {
        echo "$?"
}
```
## run sshfs-lab
```
sudo yum install sshfs
mkdir -p /scratch/home/ec2-user/sshfs-share-lab/home/aishwarya/workspace
sshfs-lab
```
# 4. genTab
## Setup genTab
```
cd $SSHFS_PIPE/code/genTab/
make clean
make

mkdir -p $LOCAL_PIPE/code/genTab/ 
cp $SSHFS_PIPE/code/genTab/genTab $LOCAL_PIPE/code/genTab/

mkdir -p $LOCAL_PIPE/data/organism/database
mkdir -p $LOCAL_PIPE/data/organism/data/ 
cp $SSHFS_PIPE/data/organism/data/protein_sequences.txt $LOCAL_PIPE/data/organism/data/ 
cp $SSHFS_PIPE/data/organism/data/genTab_org.txt        $LOCAL_PIPE/data/organism/data/
```
### run genTab
```
# srun -N 6 -n 6 hostname # to get the ips of compute node

OUT=~/slink/mpirun_genTab_1 # change the OUT directory as suited
rm -rf $OUT && mkdir -p $OUT && cd $OUT

date && \
mpirun --host \
ip-10-0-175-190:96,\
ip-10-0-170-143:96,\
ip-10-0-190-242:96,\
ip-10-0-168-94:96,\
ip-10-0-191-208:96,\
ip-10-0-187-28:96 \
$LOCAL_PIPE/code/genTab/genTab \
$LOCAL_PIPE/data/organism/data/protein_sequences.txt \
$LOCAL_PIPE/data/organism/database \
$LOCAL_PIPE/data/organism/data/genTab_org.txt \
> $OUT/output && \
date

tail -f $OUT/output
```
# 5. mp-pipe
## Setup mp-pipe2
change the code in mp-pipe2.c
```
MPI_Address --> MPI_Get_address
MPI_Type_struct --> MPI_Type_create_struct
```
Recomplie and Run
```
cd $SSHFS_PIPE/code/MP-PIPE2/
rm mp-pipe2
mpicc -O3 -fopenmp -Wall mp-pipe2.c -m64 -lm -o mp-pipe2

mkdir -p $LOCAL_PIPE/code/MP-PIPE2/             && cp $SSHFS_PIPE/code/MP-PIPE2/mp-pipe2                        $LOCAL_PIPE/code/MP-PIPE2/
mkdir -p $LOCAL_PIPE/data/organism/input/       && cp $SSHFS_PIPE/data/organism/input/*                         $LOCAL_PIPE/data/organism/input/
mkdir -p $LOCAL_PIPE/data/organism/data/        && cp $SSHFS_PIPE/data/organism/data/protein_pairs_index.txt    $LOCAL_PIPE/data/organism/data/
mkdir -p $LOCAL_PIPE/data/organism/data/        && cp $SSHFS_PIPE/data/organism/data/protein_pairs_indexes.txt  $LOCAL_PIPE/data/organism/data/
mkdir -p $LOCAL_PIPE/data/organism/data/        && cp $SSHFS_PIPE/data/organism/data/PIPE_org.txt               $LOCAL_PIPE/data/organism/data/
mkdir -p $LOCAL_PIPE/data/organism/output/

OUT=~/slink/mpirun_mp-pipe_1 # change the OUT directory as suited
cd ~/ && rm -rf $OUT && mkdir -p $OUT && cd $OUT

date && \
mpirun --host \
ip-10-0-175-190:96,\
ip-10-0-170-143:96,\
ip-10-0-190-242:96,\
ip-10-0-168-94:96,\
ip-10-0-191-208:96,\
ip-10-0-187-28:96 \
$LOCAL_PIPE/code/MP-PIPE2/mp-pipe2 \
$LOCAL_PIPE/data/organism/input/organism.in.1_pairs \
$LOCAL_PIPE/data/organism/output/organism_out_pipe_sites.tsv \
$LOCAL_PIPE/data/organism/data/protein_pairs_index.txt \
$LOCAL_PIPE/data/organism/data/protein_pairs_indexes.txt \
$LOCAL_PIPE/data/organism/database \
$LOCAL_PIPE/data/organism/data/PIPE_org.txt \
> $OUT/output && \
date

tail -f $OUT/output
```
