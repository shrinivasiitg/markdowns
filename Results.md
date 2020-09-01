# Email

# 1. Running PIPE on Protein-Dataset (homosapiens)

Latest Homo sapiens protein sequences from **Uniprot**
protein-pairs (only physical interactions) from **BioGrid**

## 1.1. Re-Running PIPE code (generating landscapes)

### 1.1.1. Re-generating simProt files

A `simProt` file for a protein sequence represents all interactions of this protein to all other proteins. This is directly loaded in memory while calculating the landscape to significantly reduce the run time of pipe for each input-pair

* **lab/Graham:** 8 CPUs
    * ~16 hrs runtime
* **AWS:** 196 CPUs (with 4GB memory each CPUs)
    * ~10 minutes

* **Output: **one ` .db ` file per protein sequence

### 1.1.2. Re-generating Landscapes

Once the ` simProt ` files are generated, we run the pipe algo to generate landscapes for each input pair

* **lab/Graham:**  8 CPUs
    * ~30 minutes
* **AWS: **on 196 CPUs (with 4GB memory each CPUs)
    * ~1 minute
* **Output : **one** **landscape (` .mat ` file) for each input pair

## 1.2. Generating PIPE-SITES (running Walk algo on landscapes)

Running walk algo to predict top-3 binding sites.

* **input :** landscape for all input pairs
* **output :** combining top-3 sites results for all input pairs in one file -` human.out `

```
protein_a   protein_b   PIPE_score  Matrix_max  running_time    sim_weighted_score  sim_weighted_score_old  site1_height    site1a_start    site1a_end  site1b_start    site1b_end  site2_height    site2a_start    site2a_end  site2b_start    site2b_end  site3_height    site3a_start    site3a_end  site3b_start    site3b_end
P05412      P01100      0.99996449  74          0.01288104      21.81304603         21.81304603             74              188             225         124             198         73              249             291         132             169         43              190             225         42              112
P05412      O43889      0.99996590  67          0.01253891      11.58697320         11.58697320             67              188             225         175             196         53              249             291         172             211         40              190             225         211             242
P05387      P05386      0.99956140  194         0.00135708      10.46458333         10.46458333             194             62              94          61              91          142             84              111         61              91          78              62              94          86              109
P05155      P00734      0.99998621  27          0.03019214      18.85990698         18.85990698             27              253             285         380             416         27              275             318         380             416         23              78              122         288             316
P05155      O00187      0.99998753  24          0.03300405      15.84075218         15.84075218             24              253             285         655             686         24              276             318         655             686         20              77              117         293             316
P05154      P03952      0.99998330  27          0.02521110      18.46808431         18.46808431             27              46              98          407             445         26              187             228         407             445         26              328             379         407             445

```

    * 
    * a
    * protein_a   protein_b   PIPE_score  Matrix_max  running_time    sim_weighted_score  sim_weighted_score_old  site1_height    site1a_start    site1a_end  site1b_start    site1b_end  site2_height    site2a_start    site2a_end  site2b_start    site2b_end  site3_height    site3a_start    site3a_end  site3b_start    site3b_end
        P05412      P01100      0.99996449  74          0.01288104      21.81304603         21.81304603             74              188             225         124             198         73              249             291         132             169         43              190             225         42              112
        P05412      O43889      0.99996590  67          0.01253891      11.58697320         11.58697320             67              188             225         175             196         53              249             291         172             211         40              190             225         211             242
        P05387      P05386      0.99956140  194         0.00135708      10.46458333         10.46458333             194             62              94          61              91          142             84              111         61              91          78              62              94          86              109
        P05155      P00734      0.99998621  27          0.03019214      18.85990698         18.85990698             27              253             285         380             416         27              275             318         380             416         23              78              122         288             316
        P05155      O00187      0.99998753  24          0.03300405      15.84075218         15.84075218             24              253             285         655             686         24              276             318         655             686         20              77              117         293             316
        P05154      P03952      0.99998330  27          0.02521110      18.46808431         18.46808431             27              46              98          407             445         26              187             228         407             445         26              328             379         407             445
        

### 1.2.1 Parameter tuning for Walk algo

* Found out the most significant parameter : **cutoff**
* Tuned this for range 0.3 to 0.9 (default was 0.5)~

# Running PIPE-SW on Protein-Dataset (homosapiens)

## 1. Understanding SW

1

# Getting most recent Validation dataset (homo sapiens)

## 1. Getting most recent Domain pairs (homo sapiens)

We got most updated `3did_flat` file from 3did source. 

* source-code :  `Accessing_3did/formatFlatFile.py`
* Input : `Accessing_3did/3did_flat`
    * 3did format
* Output : `ProteinFiles/interactingDomainPairs.txt` (latest from 3did)
    * (50% more data than the last time it was run)
    * PF10417 PF10417
        PF10417 PF00578
        PF10417 PF02195
        PF10417 PF00085
        PF12574 PF12574
        ...
        ...

## 2. Get recent Domains found for Protein sequences (human)

### 2.1. get latest Protein file to this code

get 20236 Human protein sequences (BioGrid / mp-pipe/data)

    * cp \
        $SSHFS_PIPE/data/organism/data/protein_sequences.txt_full \
        $LOCAL_PIPE_maryamkaka/ProteinFiles/protein_list.txt
        

### 
2.2. Get  proteins with domain for this list

* script : `FetchPfam/main_parallel.py`
    * takes 0.5 seconds per protein, per CPU
    * hence we need `main_parallel.py` 
        * (96 CPUs on AWS, m4.24xlarge 96 CPU, 4GB memory per CPU)
* Input : `ProteinFiles/protein_list.txt` (20236 sequences)
    * A0A024RCH0  MEKPYNKNEGNLENEGKPEDEVEPDDEGKSDEEEKPDAEGKTECEGKRKAEGEPGDEGQLEDKGSQEKQGKSEGEGKPQGEGKPASQAKPEGQPRAAEKRPAGDYVPRKAKRKTDRGTDDSPKDSQEDLQERHLSSEEMMRECGDVSRAQEELRKKQKMGGFHWMQRDVQDPFAQGDNGVSGE
        A0A075B762 MVVSAGPWSSEKAEMNILEINETLRPQLAEKKQQFRSLKEKCFLTQLAGFLANRQKKYKYEECKDLIKFMLRNERQFKEEKLAEQLKQAEELRQYKVLVHSQERELTQLREKLREGRDASRSLYEHLQALLTPDEPDKSQGQDLQEQLAEGCRLAQHLVQKLSPENDEDEDEDVQVEEAEKVLESSAPREVQKAEESKVPEDSLEECAITCSNSHGPCDSNQPHKNIKITFEEDEVNSTLVVDRESSHDECQDALNILPVPGPTSSATNVSMVVSAGPLSSEKAEMNILEINEKLRPQLAEKKQQFRNLKEKCFLTQLSGFLANQQKKYKYEECKDLIKFMLRNERQFKEEKLAEQLKQAEELRQYKVLVHAQERELTQLKEKLREGRDASRSLNEHLQALLTPYEPDKSQGQDLQEQLAEGCRLAQHLVQKLSPENDNDDDEDVQVEVAEKVQKSSAPREMQKAEEKEVPEDSLEECAITYSNSHGSYDSNQPHRKTKITFEEDKVDSTLIGSSSHVEWEDAVHIIPENESDDEEEEEKGPVSPRNLQESEEEEVPQESWDEGYSTLSIPPEMLASYQSYSSTFHSLEEQQVCMAVDIGRHRWDQVKKEDQEATGPRLSRELLDEKGPEVLQDSQDRCYSTPSGCLELTDSCQPYRSAFYILEQQRVGLAIDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQRVGFAFDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDEEEEEDQDPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEENHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLEEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQRVGFAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDEEEEEDQDPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLEEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQRVGFAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDEEEEEDQDPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQHVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQHVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQHVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQHVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDQEEEEDQDPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQHVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQHVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELTDSCQPYRSAFYVLEQQHVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDEEEEEDQDPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGYLELTDSCQPYRSAFYVLEQQHVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYGSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLSRELLDEKGPEVLQDSLDRCYSTPSGCLELCDSCQPYRSAFYVLEQQRVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLDEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYSSAVYSLEEQYLGLALDVDKIEKKGKGKKRRGRRSKKERRRGRKEGEEDQNPPCPRLNGVLMEVEEREVLQDSLDRCYSTPSMYFELPDSFQHYRSVFYSFEEQHISFALYVDNRFFTLTVTSLHLVFQMGVIFPQ
        A0A087WTN9 MAWWKSWIEQEGVTVKSSSHFNPDPDAETLYKAMKGIGVGSQLLSHQAAAFAFPSSALTSVSPWGQQGHLCCNPAGTNEQAIIDVLTKRSNTQRQQIAKSFKAQFGKDLTETLKSELSGKFERLIVALMYPPYRYEAKELHDAMKGLGTKEGVIIEILASRTKNQLREIMKAYEEDYGSSLEEDIQADTSGYLERILVCLLQGSRDDVSSFVDPGLALQDAQDLYAAGEKIRGTDEMKFITILCTRSATHLLRVFEEYEKIANKSIEDSIKSETHGSLEEAMLTVVKCTQNLHSYFAERLYYAMKGAGTRDGTLIRNIVSRSEIDLNLIKCHFKKMYGKTLSSMIMEDTSGDYKNALLSLVGSDP
        A0A087WU62 MAAPIPQGFSCLSRFLGWWSRQPVLVTQSAAIVPVRTKKRFTPPIYQPKFKTEKEFMQHARKAGLVIPPEKSDRSIHLACTAGIFDAYVPPEGDARISSLSKEGLIERTERMKKTMASQVSIRRIKDYDANFKIKDFPEKAKDIFIEAHLCLNNSDHDRLHTLVTEHCFPTLAIYDRFGRLMYGQEDVPKDVLEYVVFEKQLTNPYGSWRMHTKIVPPWAPPKQPILKTVMIPGPQLKPEEEYEEAQGEAQKPQLA
        A0A087WUF1 MVVSAGPWSSEKAEMNILEINEKLRPQLAENKQQFRNLKERCFLTQLAGFLANRQKKYKYEECKDLIKFMLRNERQFKEEKLAEQLKQAEELRQYKVLVHSQERELTQLREKLREGRDASRSLNEHLQALLTPDEPDKSQGQDLQEQLAEGCRLAQQLVQKLSPENDEDEDEDVQVEEDEKVLESSAPREVQKAEESKVPEDSLEECAITCSNSHGPCDSIQPHKNIKITFEEDKVNSTVVVDRKSSHDECQDALNILPVPGPTSSATNVSMVVSAGPLSSEKAEMNILEINEKLRPQLAEKKQQFRSLKEKCFVTQLAGFLAKQQNKYKYEECKDLIKSMLRNELQFKEEKLAEQLKQAEELRQYKVLVHSQERELTQLREKLREGRDASRSLNEHLQALLTPDEPDKSQGQDLQEQLAEGCRLAQHLVQKLSPENDEDEDEDVQVEEDEKVLESSSPREMQKAEESKVPEDSLEECAITCSNSHGPCDSNQPHKNIKITFEEDKVNSSLVVDRESSHDECQDALNILPVPGPTSSATNVSMVVSAGPLSSEKAEMNILEINEKLRPQLAEKKQQFRSLKEKCFVTQVACFLAKQQNKYKYEECKDLLKSMLRNELQFKEEKLAEQLKQAEELRQYKVLVHSQERELTQLREKLREGRDASRSLNEHLQALLTPDEPDKSQGQDLQEQLAEGCRLAQHLVQKLSPENDNDDDEDVQVEVAEKVQKSSSPREMQKAEEKEVPEDSLEECAITCSNSHGPYDSNQPHRKTKITFEEDKVDSTLIGSSSHVEWEDAVHIIPENESDDEEEEEKGPVSPRNLQESEEEEVPQESWDEGYSTLSIPPERLASYQSYSSTFHSLEEQQVCMAVDIGRHRWDQVKKEDQEATGPRLSRELLAEKEPEVLQDSLDRCYSTPSVYLGLTDSCQPYRSAFYVLEQQRVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLAEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYRSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYRSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKKRRRGRKEGEEDQNPPCPRLSRELLAEKEPEVLQDSLDRWYSTPSVYLGLTDPCQPYRSAFYVLEQQRVGLAVDMDEIEKYQEVEEDQDPSCPRLSRELLAEKEPEVLQDSLDRCYSTPSGYLELPDLGQPYRSAVYSLEEQYLGLALDVDRIKKDQEEEEDQGPPCPRLSRELLEVVEPEVLQDSLDRCYSTPSSCLEQPDSCQPYRSSFYALEEKHVGFSLDVGEIEKKGKGKKRRGRRSKKKRRRGRKEGEEDQNPPCPRLNSVLMEVEEPEVLQDSLDRCYSTPSMYFELPDSFQHYRSVFYSFEEQHITFALDMDNSFFTLTVTSLHLVFQMGVIFPQ
        ...
        ...
* Output : `ProteinFiles/ProteinPfam.txt` (domains present for all 20236 sequences)
    * A0A024RCH0 PF04538
        A0A075B762 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758
        A0A087WTN9 PF00191 PF00191 PF00191 PF00191
        A0A087WU62 PF04280
        A0A087WUF1 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758 PF06758
        ...
        ...
* How to run (takes 2-3 minutes, earlier was taking 20236/2 seconds ~ 3 hrs

```
# add this in pfamConnect function in getPfam.py
    try :
        for i in range(0, len(xml.pfam.entry.matches.match)):
            pfam.append(xml.pfam.entry.matches.match[i]['accession'])
    except :
        try:
            pfam.append(xml.pfam.entry.matches.match['accession'])
        except :
            # skip the protein for which entry not found in pfam 
            # http://pfam.xfam.org/protein/A0A024RCH0?output=xml
            pfam.append(' ')

# FetchPfam/main_parallel.py 
from getPfam import pfamConnect as getPfam

import math
import pickle
import pandas as pd
import multiprocessing as mp
from multiprocessing import Pool

outputFile = 'ProteinFiles/ProteinPfam.txt'
inputFile = 'ProteinFiles/protein_list.txt'

def updatedGetPfam(protein):
    return protein + ' ' + ' '.join(getPfam(protein))

print("prcoessing parallel")
df = pd.read_csv(inputFile, header=None, delim_whitespace=True )

cpus = math.ceil(mp.cpu_count() * 2)
print("Using CPUs = ", cpus)
p = Pool(cpus)
protein_with_domain = p.map(updatedGetPfam, df[0].tolist())

print('writing pkl to ', outputFile + '.pkl')
with open(outputFile + '.pkl', 'wb') as f:
    pickle.dump(protein_with_domain, f)
    
print('writing output to ', outputFile)
with open(outputFile, 'w') as f:
    for item in protein_with_domain:
       f.write(item + '\n')

print("writing finished")
```

```
cd $LOCAL_PIPE_maryamkaka
# (base) (Mon Aug 10 13:17:04) <2> [/nfs/workspace/maryamkaka-PIPE]
python FetchPfam/main_parallel.py
# (base) (Mon Aug 10 13:18:27) <0> [/nfs/workspace/maryamkaka-PIPE]
```

## 3. Generate multi/single/no domain

### 3.3. Upload new known interacting Protein pairs to this code

* 66084 Human protein_pairs

```
cp \
$SSHFS_PIPE/data/organism/data/protein_pairs.txt_full \
$LOCAL_PIPE_maryamkaka/ProteinFiles/protein_pairs.txt
```

### Run :` InteractionLists/main.py`

* Input: 
    * `ProteinFiles/interactingDomainPairs.txt`
    * `ProteinFiles/ProteinPfam.txt`
    * `protein_pairs` (66084 for human)
* outputs are:
    * `InteractionLists/singleDomain.txt` (5406 pairs)
        * ProteinA ProteinB DomainA DomainB
            O00139 O00139 PF00225 PF00225
            O00204 O00204 PF00685 PF00685
            O00268 O00268 PF07531 PF07531
            ...
            ...
    * `InteractionLists/noDomain.txt` (55357 pairs)
        * A0AVI4 A0AVI4
            A0MZ66 A0MZ66
            A8K8P3 A8K8P3
            O00161 O00161
            ...
            ...
    * `InteractionLists/multiDomain.txt`  (5322 pairs)
        * A6NLU0 A6NLU0
            O00141 O00141
            O00148 O00148
            O00180 O00180
            ...
            ...
* How to run

```
cd $LOCAL_PIPE_maryamkaka
python InteractionLists/main.py 
```

## 4. Get the domain details for single domain interaction

* script : `InteractionLists/stats/domainLength-parallel.py`
* Input : `InteractionLists/singleDomain.txt` (5406 pair)
* Output : `ProteinFiles/length.txt` (3324 pairs)
    * Protein Domain ProteinLength DomainLength DomainStart DomainEnd
        Q13263 PF00628 835 45 627 672
        P42226 PF01017 847 107 161 268
        Q9NYK1 PF01582 1049 158 890 1048
        ...
        ...
* How to run :

```
# InteractionLists/stats/domainLength-parallel.py

import untangle
import collections
import pandas as pd
import pickle
import math
import multiprocessing as mp
from multiprocessing import Pool
import json

def pfamConnect(protein):
    url = 'http://pfam.xfam.org/protein/'

    xml = untangle.parse(url + protein + '?output=xml').pfam.entry

    proteinLength = int(xml.sequence['length'])
    domainLength = {}

    #extract data
    try:
        for i in range(0, len(xml.matches.match)):
            current = xml.matches.match
            start = int(current[i].location['start'])
            end = int(current[i].location['end'])
            domainLength[current[i]['accession']] =  [start, end, end - start]
    except :
        try :
            start = int(xml.matches.match.location['start'])
            end = int(xml.matches.match.location['end'])
            domainLength[xml.matches.match['accession']] =  [start, end, end - start]
        except :
            return [proteinLength, domainLength]
    return [proteinLength, domainLength]


def parallelPfamConnect(protein_domain):
    protein, domain = protein_domain.split('-')[0], protein_domain.split('-')[1]
    [pLen, dLen] = pfamConnect(protein)
    ret = ''
    if(domain in dLen):
        ret = protein + '\t' + domain + '\t' + str(pLen) + '\t' + str(dLen[domain][2]) + '\t' + str(dLen[domain][0]) + '\t' + str(dLen[domain][1])
    else:
        ret = protein + '\t' + domain + '\t' + 'not-found'
    return ret


inputFile = 'InteractionLists/singleDomain.txt'
outputFile = 'ProteinFiles/length.txt'
notFoundFile = outputFile + '_notfound'

df = pd.read_csv(inputFile, delim_whitespace=True)
cpus = math.ceil(mp.cpu_count() * 2)
print("Using CPUs = ", cpus)
p = Pool(cpus)

print("Running on CPUs")
output = p.map(parallelPfamConnect, list(set((df['ProteinA'] + '-' + df['DomainA']).values.tolist() + (df['ProteinB'] + '-' + df['DomainB']).values.tolist())))

print('writing pkl to ', outputFile + '.pkl')
with open(outputFile + '.pkl', 'wb') as f:
    pickle.dump(output, f)

print('writing output to ', outputFile)
with open(outputFile, 'w') as f:
    f.write('Protein\tDomain\tProteinLength\tDomainLength\tDomainStart\tDomainEnd\n')
    for item in output:
        if not 'not-found' in item:
            f.write(item + '\n')

print('writing output to ', notFoundFile)
with open(notFoundFile, 'w') as f:
    f.write('Protein\tDomain\tFound?\n')
    for item in output:
        if 'not-found' in item:
            f.write(item + '\n')

print("writing finished")
```

### 4.2. Run `InteractionLists/stats/stats.py`

Inputs are:

* `InteractionLists/singleDomain.txt`
* `ProteinFiles/length.txt`

outputs are:

* `InteractionLists/stats/Proteins.png`
* `InteractionLists/stats/Domains.png`
* `InteractionLists/stats/DomainLength.png`
* `InteractionLists/stats/DomainLengthPercent.png`
* `InteractionLists/stats/stats.log`

change value of bins to generate for different bins

## 5. Run DM values

```
# echo "Protein1    Protein2    PIPE_score    Matrix_max    running_time    sim_weighted_score    site1_height    site1a_start    site1a_end    site1b_start    site1b_end    site2_height    site2a_start    site2a_end    site2b_start    site2b_end    site3_height    site3a_start    site3a_end    site3b_start    site3b_end" > $LOCAL_PIPE_maryamkaka/pipe_results/output/yeast.out

echo "protein_a    protein_b    PIPE_score    Matrix_max    running_time    sim_weighted_score    sim_weighted_score_old    site1_height    site1a_start    site1a_end    site1b_start    site1b_end    site2_height    site2a_start    site2a_end    site2b_start    site2b_end    site3_height    site3a_start    site3a_end    site3b_start    site3b_end" > $LOCAL_PIPE_maryamkaka/pipe_results/output/yeast.out

cat $LOCAL_PIPE/code/scripts/loocv/output_yeast/p.out >> $LOCAL_PIPE_maryamkaka/pipe_results/output/yeast.out

```

### Convert `InteractionLists/singleDomain.txt` to `InteractionLists/singleDomainPairs.txt`

```
prediction = pandas.read_table('../InteractionLists/singleDomain.txt')
prediction[['ProteinA','ProteinB']].to_csv('../InteractionLists/singleDomainPairs.txt', index=False, sep='\t', header=False)

```

