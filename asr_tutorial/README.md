# **Ancestral Sequence Reconstruction Tutorial**

1. Installing requirements
2. Accessing the data
3. Multiple sequence alignment
4. Phylogeny reconstruction
5. Ancestral sequence inference


## **1. Installing requirements**
1. Make a directory for ASR tutorial 
```
mkdir asr
cd asr 
```
2. Install Python3 - to use python scripts and miniconda.
Download from : https://www.python.org/downloads/ based on your OS

3.  Miniconda3 - to install required softwares easily.
Download from https://docs.conda.io/en/latest/miniconda.html based on your OS: 

    <details>
    <summary>for Mac</summary>

    ```
    bash Miniconda3-latest-MacOSX-x86_64.sh
    ```
    
    </details>

    <details>
    <summary>for Linux</summary>

    ```
    bash Miniconda3-latest-Linux-x86_64.sh
    ```
    </details>

    <details>
    <summary>for Windows</summary>
    
    Double click .exe file and follow installation instructions. 

    </details>


For more installation directions, see: https://conda.io/projects/conda/en/latest/user-guide/install/index.html

4. MAFFT v7.5 - to align sequences

```
conda install -c bioconda mafft
```
5. IQTREE v2.0 - to construct phylogenetic tree
```
conda install -c bioconda iqtree
```
6. PAML v4.9 - to reconstruct ancestral sequences\
Download _paml4.9j.tgz_ from http://abacus.gene.ucl.ac.uk/software/paml.html \
Move _paml4.9j.tgz_ under your asr folder 
```{bash, eval=FALSE}
mv /path-to-downloads-folder/paml4.9j.tgz /path-to-folder/asr
tar xf paml4.9j.tgz
cd paml4.9j
rm bin/*.exe
cd src
make -f Makefile
ls -lF
rm *.o
mv baseml basemlg codeml pamp evolver yn00 chi2 ../bin
cd ..
ls -lF bin
bin/baseml  # checking if "baseml" works
bin/codeml  # checking if "codeml" works
bin/evolver # checking if "evolver" works
cd ..       # go back under asr folder
```
7. Download _paml_asr.py_ . This python script is going to be used to infer insertions-deletions in ancestral sequences. 

```
wget https://github.com/evrimfer/asr_tutorial/blob/39830372a7c6c89713ef944f1fe4aeb38551ef62/paml_asr.py
pwd     # look at the current path you are in and copy it
# the path look like similar to this
/User/name/desktop/asr
```

- Open _paml_asr.py_ in a text editor and paste the path you copied  above to the line 12. Should look like this:

```
11 # set default executables and model #
12 PAMLBS = "/User/name/desktop/asr/paml4.9j" #change the path
13 CODEML = PAMLBS + "/bin/codeml"
14 BASEML = PAMLBS + "/bin/baseml"
15 SUBMOD_NAME = PAMLBS + "/dat/lg.dat"
```
Save the file. 

8. SeaView v5.0.4- to visualize sequence and alignment data.\
Download from http://doua.prabi.fr/software/seaview\

9. FigTree v1.4.4 - to visualize phylogenetic tree.\
Download from https://github.com/rambaut/figtree/releases\


## **2. Accessing The Data**
1. Download the Rubisco small subunit protein sequences using command line.

```
#check if you are under "asr" folder

pwd
/User/name/desktop/asr  

# download the data under "asr" folder
wget https://github.com/evrimfer/asr_tutorial/blob/65f81b875123a8d7fd31cbe0ae25b59021fffd98/rbsc_aa.fasta
```

## **3. Multiple Sequence Alignment**
1.  Align Rubisco sequences using MAFFT.
```
mafft  --auto --inputorder rbsc_aa.fasta > rbsc_aa_aligned.fasta
```
- The breakdown of this command is:\
``` mafft ``` starts the program MAFFT\
```--auto``` automatically selects an appropriate alignment algorithm from L-INS-i, FFT-NS-i and FFT-NS-2, according to data size.\

    <details>
        <summary>Alignment algorithms</summary>

        L-INS-i: Iterative refinement method (<16) with LOCAL pairwise alignment information\
        FFT-NS-i: (iterative refinement method; max. 1000 iterations)\
        FFT-NS-2: (fast; progressive method)\
        
    </details>

    ```--inputorder``` output order is same as input.\
    ```rbsc_aa.fasta``` is the input file name. Place it before " > " and after all flags.\
    ```rbsc_aa_aligned.fasta``` places the output alignment in thw file named rbsc_aa_aligned.fasta. ">" designates where to place the output.\

2.  Open SeaView program (or any other alignment visualization tool). Open the rubisco alignment file (File > Open > rbsc_aa_aligned.fasta). 

<details>
<summary> Alignment </summary>

![Semantic description of image](alignment.png "Image Title")

</details>

> Can you differentiate Group-A, Group-B, Group-CD sequences by looking at the alignment?\
What is the alignment length?

## **4. Phylogeny Reconstruction**
1. We can reconstruct a maximum-likelihood (ML) tree for the Rubisco Small Subunit dataset. Assuming that you are in the same folder where the alignment is stored, use the following command:

```
iqtree -s rbsc_aa_aligned.fasta -m MFP -msub nuclear -st AA -alrt 1000 -B 1000 -T AUTO
```
- The breakdown of this command is:\
```-s <filename> ``` alignment filename\
```-m MFP``` perform Model Finder Plus to find model and continue to use that model\
```-msub nuclear ``` restrict to nuclear models\
```-st AA``` specify the data is amino acid\
```-alrt 1000``` calculate likelihood ratio test for 1000 times\
```-B 1000 ``` calculate UFBoot scores for 1000 times\
```-T ``` optimize number of threads\

2. Open _rbsc_aa_aligned.fasta.log_ by clicking on the file (manually) or on command line like this:
```{bash, eval=FALSE}
less rbsc_aa_aligned.fasta.log
```
> What is the selected best evolutionary model?
    <details>
            <summary> Hint </summary> 
            Look at the line starts with "Best-fit model:"     
    </details> \
    What is the log-likelihood score of the best tree? 
    <details>
            <summary> Hint </summary> 
            Look at the line starts with "BEST SCORE FOUND :"    
    </details>


3. Open FigTree and load the _rbsc_aa_aligned.fasta.treefile_ for visualization (File > Open > rbsc_aa_aligned.fasta.treefile). 
The tree is unrooted. We can root the tree by selecting the branch including Group-CD sequences and click on "Reroot" option at the top menu panel. 

4. To see branch support values, check **_Branch Labels_** on the menu at the left side. Click on the triangle next to **_Branch Labels_** to see drop-down menu. Click on the drop down menu of **_Display_** and choose **_label_**. You will see numbers divided by "/" on the tree branches. The first numbers are likelihood ratio test (SH-aLRT) values (%) , second numbers are ultrafast bootstrap support (%). Ideally, the branches having SH-aLRT > 80 and UFBoot > 95 are considered as reliable. 

5. See more information about bootstrap support: http://www.iqtree.org/doc/Frequently-Asked-Questions#how-do-i-interpret-ultrafast-bootstrap-ufboot-support-values

<details>
<summary> Tree </summary>

![Semantic description of image](rbsc_tree.png "Image Title")

</details>
> Are the rubisco sequences monophyletic based on groups?


## **5. Ancestral Sequence Inference**
1. Using the alignment and tree file, we can infer the ancestral sequences with PAML. We use _paml_asr.py_ python script to infer indel regions. 

2. First define the path of paml4.9. Then, reconstruct ancestral sequences using the following commands python command. 

```
export PATH=/User/name/asr/paml4.9j/bin:$PATH  #change the paml path according to your path
python3 paml_asr.py rbsc_aa_aligned.fasta  rbsc_aa_aligned.fasta.treefile --noclean
```

- You should have the following outputs:\
_rbsc_aa_aligned.asr.mlseqs.fasta_ - ancestral sequences in alignment format\
_rbsc_aa_aligned.asr.probdists.csv_ - posterior probability matrix showing probabilities of each amino acid per site\
_rbsc_aa_aligned.asr.tre_ - tree file with the numbers of ancestral nodes

2. Open _rbsc_aa_aligned.asr.tre_ with FigTree. To see labels of ancestral node numbers, check **_Branch Labels_** on the menu at the left side. Click on the triangle next to **_Branch Labels_** to see drop-down menu. Click on the drop down menu of **_Display_** and choose **_label_**. You will see the node labels on the tree. 

3. You can check the label of the interested ancestral node and find the inferred sequence of that node in _rbsc_aa_aligned.asr.mlseqs.fasta_ file. 

<details>
<summary> ASR Tree </summary>

![Semantic description of image](rbsc_asrtree.png "Image Title")

</details>

> What is the node number of the common ancestor of Group-A and Group-B rubisco sequences?\
What is the inferred sequence of this ancestral node? 