```python 
creating loocv folder structure...
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/cutoffs
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/roc_curves
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/cutoffs
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/roc_curves
mkdir /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output
creating positive and negative input files...
/storage/aschoenr/PIPE/code/scripts/convert_names_to_IDs.py /storage/aschoenr/PIPE/data/s.cerevisiae/data/protein_pairs.txt /storage/aschoenr/PIPE/data/s.cerevisiae/data/protein_pairs_index.txt temp
rm temp
/storage/aschoenr/PIPE/code/create_roc/create_random_ID_pairs.py /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/p.in 20236 100000 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/n1.in
/storage/aschoenr/PIPE/code/create_roc/create_random_ID_pairs.py /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/p.in 20236 100000 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/n2.in
/storage/aschoenr/PIPE/code/create_roc/create_random_ID_pairs.py /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/p.in 20236 100000 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/n3.in
updating and recompiling MP-PIPE code...
mpicc -O3 -fopenmp -Wall /storage/aschoenr/PIPE/code/MP-PIPE2/mp-pipe2_loocv.c -m64 -lm -o /storage/aschoenr/PIPE/code/MP-PIPE2/mp-pipe2_loocv
mpicc -O3 -fopenmp -Wall /storage/aschoenr/PIPE/code/MP-PIPE2/mp-pipe2_loocv.c -m64 -lm -o /storage/aschoenr/PIPE/code/MP-PIPE2/mp-pipe2_loocv
/storage/aschoenr/PIPE/code/scripts/update.py /storage/aschoenr/PIPE /tmp/aschoenr/ /storage/aschoenr/PIPE/code/MP-PIPE2/PIPE_hosts
mpirun -np 8 -hostfile /storage/aschoenr/PIPE/code/MP-PIPE2/PIPE_hosts /tmp/aschoenr/PIPE/code/MP-PIPE2/mp-pipe2_loocv /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/p.in /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/p.out /tmp/aschoenr/PIPE/data/s.cerevisiae/data/protein_pairs_index.txt /tmp/aschoenr/PIPE/data/s.cerevisiae/database
mpirun -np 8 -hostfile /storage/aschoenr/PIPE/code/MP-PIPE2/PIPE_hosts /tmp/aschoenr/PIPE/code/MP-PIPE2/mp-pipe2_loocv /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/n1.in /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n1.out /tmp/aschoenr/PIPE/data/s.cerevisiae/data/protein_pairs_index.txt /tmp/aschoenr/PIPE/data/s.cerevisiae/database
mpirun -np 8 -hostfile /storage/aschoenr/PIPE/code/MP-PIPE2/PIPE_hosts /tmp/aschoenr/PIPE/code/MP-PIPE2/mp-pipe2_loocv /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/n2.in /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n2.out /tmp/aschoenr/PIPE/data/s.cerevisiae/data/protein_pairs_index.txt /tmp/aschoenr/PIPE/data/s.cerevisiae/database
mpirun -np 8 -hostfile /storage/aschoenr/PIPE/code/MP-PIPE2/PIPE_hosts /tmp/aschoenr/PIPE/code/MP-PIPE2/mp-pipe2_loocv /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/input/n3.in /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n3.out /tmp/aschoenr/PIPE/data/s.cerevisiae/data/protein_pairs_index.txt /tmp/aschoenr/PIPE/data/s.cerevisiae/database
processing PIPE output files...
cut -f1,2,3 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/p.out > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/p.out
cut -f1,2,6 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/p.out > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/p.out
cut -f1,2,3 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n1.out > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/n1.out
cut -f1,2,6 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n1.out > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/n1.out
cut -f1,2,3 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n2.out > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/n2.out
cut -f1,2,6 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n2.out > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/n2.out
cut -f1,2,3 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n3.out > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/n3.out
cut -f1,2,6 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/output/n3.out > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/n3.out
creating roc curve files...
cp /storage/aschoenr/PIPE/code/create_roc/roc ./
./roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/p.out 66084 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/n1.out 100000  > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/cutoffs/n1.cutoffs
mv results.roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/roc_curves/n1.roc
./roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/p.out 66084 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/n1.out 100000  > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/cutoffs/n1.cutoffs
mv results.roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/roc_curves/n1.roc
./roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/p.out 66084 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/n2.out 100000  > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/cutoffs/n2.cutoffs
mv results.roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/roc_curves/n2.roc
./roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/p.out 66084 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/n2.out 100000  > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/cutoffs/n2.cutoffs
mv results.roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/roc_curves/n2.roc
./roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/p.out 66084 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/stripped_output/n3.out 100000  > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/cutoffs/n3.cutoffs
mv results.roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/roc_curves/n3.roc
./roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/p.out 66084 /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/stripped_output/n3.out 100000  > /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/cutoffs/n3.cutoffs
mv results.roc /storage/aschoenr/PIPE/data/s.cerevisiae/loocv/SW_score/roc_curves/n3.roc
rm ./roc
creating roc curve...
cp /storage/aschoenr/PIPE/code/create_roc/loocv.plt ./
gnuplot ./loocv.plt
creating cutoff files...
Traceback (most recent call last):
  File "loocv.py", line 206, in <module>
    cutoff_file = open(local_dir + 'PIPE/data/' + organism_name + '/loocv/PIPE_score/cutoffs/cutoffs.txt', 'w')
IOError: [Errno 2] No such file or directory: '/storage/aschoenr/PIPE/data/s.cerevisiae/loocv/PIPE_score/cutoffs/cutoffs.txt'
```
