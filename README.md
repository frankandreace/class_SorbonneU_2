# 2022-pangenome-graphs-intro

Practical session for the Pangenomics and Metagenomics class given at Sorbonne University in Paris, December 15th 2022.

FOr the teoretical part, I used a lot of material from Rayan Chiki's lecuture at WABI2022.

Francesco's slides: https://docs.google.com/presentation/d/1AY0s1vnbb0TYTet9nJlSoVlg0zBnpdnwM_CxKg3suZ0/edit?usp=sharing   


## Rationale

We'll see how we can use some tools for metagenomic assemby and binning and discuss about the differences!

## Data

Data is two simple simulated metagenomic dataset, S1 with just 2 species, S5 with 3, present in the `data/` folder.
Their are parie-end short reads!

## Output
Let's have a folder where to store the output of the tools

    mkdir ouput

## TOOLS
If you don't have conda, please install it!

Generate the conda environment from the text file:

    conda env create --file environment.yaml

Activation of the environment on Windows:

    activate [env_name] (class_SU_2?)

Activation of the environment on Linux/Mac:

    source activate [env_name]

Now have a look at how tools work:   
minimap2 webpage: https://github.com/lh3/   
miniasm webpage: https://github.com/lh3/miniasm
bcalm webpage: https://github.com/GATB/bcalm   
spades webpage: https://github.com/ablab/spades#meta   
maxbin2 webpage: https://github.com/assemblerflow/flowcraft/blob/master/docs/user/components/maxbin2.rst

Or reading the helpme of the tools.

    minimap2
    miniasm
    bcalm
    spades.py
    maxbin2

## mapping reads in the sample


    minimap2 -X -sr -B8 -O12,32 -E2,1 -m40 -k21 -w10 --end-bonus=100 [input_fasta] [input_fasta] > [output_name.paf] 

## assembling the mapped reads into contigs


    miniasm -12 -m1 -g10000 -o1 -d1 -I0.00001 -s1 -i0.00001 -d10000 -F0.01 -c0 -e0 -n0 -r0.011,0.01 -f [input_fasta] [input_paf] > [output_name.gfa]

## let's have a look at the gfa with Bandage


    Bandage load [file.gfa]

## let's now build a de bruijn graph with the reads
With bcalm like last lesson!


    bcalm -in [reads.fa] -kmer-size [kmer_size] -abundance-min [abundance_threshold]

    python3 to_gfa.py [input.uinitgs.fa] [input.unitigs.gfa] [kmer_size]

Now let's have a look at it with Bandage!

## Let's now try spades!!!


    spades.py --meta --only-assembler -1 [reads.fa] -2 [reads.fa] 

Which is the output? What does it do? Use Bandage!

## Let's try megahit!


    megahit -1 [reads1.fa] -2 [reads2.fa] -o [output]

## let's run metabat2
First, we need sorted bams! 
Let's map the reads to the contigs using bwa-mem2

INDEXING THE CONTIGS

    ./bwa-mem2 index [-p prefix] <in.fasta>
Where 
<in.fasta> is the path to reference sequence fasta file and 
<prefix> is the prefix of the names of the files that store the resultant index. Default is in.fasta.

MAPPING THE READS TO THE CONTIGS

    ./bwa-mem2 mem -t <num_threads> <prefix> <reads.fq/fa> > out.sam
Where <prefix> is the prefix specified when creating the index or the path to the reference fasta file in case no prefix was provided.

Now convert the sam to bam and sort the bam!

    samtools view -S -b [samfile] > [bamfile]

    samtools sort [bamfile] -o [sorted_bam]

Now let's run MetaBat2!

    runMetaBat.sh [contigs] [sorted_bam]



