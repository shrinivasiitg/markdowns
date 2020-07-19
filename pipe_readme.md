# Login to sharcnet

# Working on pipe

# Checkout git repo (on lab machine)
I've checked out the git repo under ```~/workspace/``` directory hence after checking-out/downloading, this is how my dir structure looks like:
```shell script
ls -ltrh ~/workspace/PIPE/PIPE/

drwxrwxrwx 3 aishwarya aishwarya 4.0K Jul 18 18:31 data
drwxrwxrwx 7 aishwarya aishwarya 4.0K Jul 18 18:31 code
``` 
## 1. Create small test set
* Make sure the file ```~/workspace/PIPE/PIPE/data/organism/data/create_test_small.py``` has the following settings:
```python
num_prots = 1000 # limiting total number of proteins to be used as 100
num_test = 10000 # limiting number of protein-pairs to be used as 10000 to reduce the run-time of 'small' experiment
```  
Then, run following:
```shell script
cd ~/workspace/PIPE/PIPE/data/organism/data/
python ~/workspace/PIPE/PIPE/data/organism/data/create_test_small.py
```
## 2. Configure ```genTab```
### 2.1. ```convertPairs.pl```
Make sure that the file ```~/workspace/PIPE/PIPE/code/genTab/convertPairs.pl``` has the correct complete path (```absolute path```) for the first three files, like this:
```perl
$SEQ_FILE="/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/protein_sequences.txt";
$STR_PAIR_FILE="/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/protein_pairs.txt";
$IDX_PAIR_FILE="/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/protein_pairs_index.txt";
```
Now run
```shell script
cd ~/workspace/PIPE/PIPE/code/genTab/
./convertPairs.pl
```
This generates the file ```$IDX_PAIR_FILE``` (mentioned above) at the location ```/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/protein_pairs_index.txt``` (as provided, yay!!)

### 2.2. disable ```inline``` functionality
Just remove all the occurence of the exact keyword ```inline``` from these two files (that stops ```inline``` functionality)
1. ```~/workspace/PIPE/PIPE/code/genTab/PIPE.h```
1. ```~/workspace/PIPE/PIPE/code/genTab/routines.c```

This is most likely **NOT** an option (must do it !!)

### 2.3. Verify params in ```PIPE.h```
Get these two numbers:
1. number of lines in the protein_sequences.txt file = ```wc -l ~/workspace/PIPE/PIPE/data/organism/data/protein_sequences.txt```
1. number of lines in the protein_pairs.txt file = ```wc -l ~/workspace/PIPE/PIPE/data/organism/data/protein_pairs.txt```

Make sure in the file ```~/workspace/PIPE/PIPE/code/genTab/PIPE.h```, these param looks something like this:
```c
/* Constant Defines used throughout code */
#define MAX_LINE_LEN 24277
#define MAX_PROTEIN_LEN 24177
#define MAX_NUM_PROTEINS 1000 /* number of lines in the protein_sequences.txt file */
#define MAX_NEIGHBOURS 3000
#define W 20       /* Length of segment */
#define SCORE 35  /* PAM Score threshold */
```

The only possible change is the ```MAX_NUM_PROTEINS``` to ```1000``` since we set this in ```~/workspace/PIPE/PIPE/data/organism/data/create_test_small.py``` earlier.

### 2.4. Verify ```genTab_org.txt```
For file ```~/workspace/PIPE/PIPE/data/organism/data/genTab_org.txt```, no need to change the existing values, which should look like this:
```text
1
1000
1
0

```
This represents:
```text
1       --> number of species
1000    --> number of proteins
1       --> number of species to generate a landscape for
0       --> index of species 1
        --> new line at the end of file is perhaps necessary (dunn know)
```

In general this represents:
```text
line 1      : <Number of species>
line 2      : <Number of proteins for species 1>
line 3      : <Number of proteins for species 2>
            : ...
line n+1    : <Number of proteins for species n>
line n+2    : <Number of species we want to generate files for>
line n+3    : <Index of species 1>
line n+4    : <Index of species 2>
            : ...
line last   : <Index of species n>         
```
### 2.5. Verify ```parameters.txt```
For file ```~/workspace/PIPE/PIPE/data/organism/parameters.txt```, change the existing values to reflect the small testing we are doing. New values (strictly for small testing) should look like this:
```text
num proteins	1000
num known pairs	504
num pairs	500500
max_db_file	175036
num_set_bits	1024
max_protein_len	5537
max_neighbours	16

```
This represents:
```text
num proteins	1000    --> number of lines in the protein_sequences.txt file
num known pairs	504     --> number of lines in the protein_pairs.txt file    
num pairs	500500      --> keep the default value  
max_db_file	175036      --> keep the default value, these numbers need to be bigger
num_set_bits	1024    --> keep the default value
max_protein_len	5537
max_neighbours	16
                        --> new line at the end of file is perhaps necessary (dunn know) 
```
### 2.6. Verify ```PIPE_org.txt```
For file ```~/workspace/PIPE/PIPE/data/organism/data/PIPE_org.txt```, no need to change the existing values, which should look like this:
```text
1
1
0,0

```
```text
1       --> <Number of species>
1       --> <Number of training datasets>
0,0     --> <index of training dataset of species>, <index of training dataset of species>
        --> new line at the end of file is perhaps necessary (dunn know) 
```
 
## 3. Run ```genTab``` (```simProt``` for each Protein)
This step is responsible for the pre processing of the PIP algorithm which generates the ```simProt``` files for each Protein 

* Make sure the directory got permissions, or run this command ```chmod -R ugo+rwx ~/workspace/PIPE/PIPE/```. 
* Cleanup the existing ```genTab``` followed by running it: 
```shell script
cd ~/workspace/PIPE/PIPE/code/genTab/
make clean
make
```
* Create a database dir (if not already exists) by running ```mkdir -p ~/workspace/PIPE/PIPE/data/organism/database/```. This will be used as the output for the pre processing (the ```simProt``` db files per protein)
* Get the number of **CPU cores** for the machine using ```nproc --all```. This comes out to be **```8```** for the lab/UNIX machine.
* Run the ```genTab```:
```shell script
cd ~/workspace/PIPE/PIPE/code/genTab/

mpirun -n 8 /home/aishwarya/workspace/PIPE/PIPE/code/genTab/genTab \
/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/protein_sequences.txt \
/home/aishwarya/workspace/PIPE/PIPE/data/organism/database \
/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/genTab_org.txt \
> mpirun_8_genTab_output & disown 
```
* Check the progress: ```tail -f ~/workspace/PIPE/PIPE/code/genTab/mpirun_8_genTab_output``` 

For future reference, we have stored the database for all the proteins at ```/home/aishwarya/Downloads/database```, so that we don't have to run ```genTab``` again (which took ~10 hrs to run for all proteins)

## 4. Run PIPE prediction
* Verify ```mp-pipe2.c``` that the file ```~/workspace/PIPE/PIPE/code/MP-PIPE2/mp-pipe2.c``` (around line# ```59```), no need to change the existing values, which should look like this:
```c
static const int WINDOW_SIZE = 20;
static const int FILTER_SIZE = 3;
static const double THRESHOLD = 0;
static const int PACKET_SIZE = 1000;
int THREADS = 2; /* number of logical processors to be used, this is easily changed in the run command */
```
* Run ```mp-pipe2``` :
```shell script
cd ~/workspace/PIPE/PIPE/code/MP-PIPE2/
rm mp-pipe2
mpicc -O3 -fopenmp -Wall mp-pipe2.c -m64 -lm -o mp-pipe2
```
* Run PIPE
```shell script
rm -rf /home/aishwarya/Downloads/mpirun_8_mp-pipe2_20236
mkdir -p /home/aishwarya/Downloads/mpirun_8_mp-pipe2_20236
cd /home/aishwarya/Downloads/mpirun_8_mp-pipe2_20236

mpirun -n 8 /home/aishwarya/workspace/PIPE/PIPE/code/MP-PIPE2/mp-pipe2 \
/home/aishwarya/workspace/PIPE/PIPE/data/organism/input/organism.in \
/home/aishwarya/workspace/PIPE/PIPE/data/organism/output/organism_out.tsv \
/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/protein_pairs_index.txt \
/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/protein_pairs_indexes.txt \
/home/aishwarya/Downloads/database \
/home/aishwarya/workspace/PIPE/PIPE/data/organism/data/PIPE_org.txt \
> /home/aishwarya/Downloads/mpirun_8_mp-pipe2_20236/mpirun_8_mp-pipe2_output & disown
```


* Check the progress: ```tail -f /home/aishwarya/Downloads/mpirun_8_mp-pipe2_20236/mpirun_8_mp-pipe2_output```
