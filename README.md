# MBATC_Assembly

by Samuel Abalde & Iker Irisarri (MNCN, CSIC)



This is an introductory tutorial to assembling high throughput sequencing (Illumina) data. We will assemble mitochondrial genomes and transcriptomes of cone snails. 

Let's start by cloning this GitHub repository. It can also be downloaded as zip file in the button "<>code".

```
git clone https://github.com/iirisarri/MBATC_Assembly.git
```

The folder named `Data` contains:
- db_mtDNA_genes: database containing mitochondrial genes from cone snails
- Mitochondrial: genomic data from *Lautoconus ventricosus*, mostly limited to the mitochondrial genome.
- RNA: transcriptomic data from two species of cone snail (*Lautoconus ventricosus* and *Chelyconus ermineus*).

We will use the following software (under `/home/demo-user/demosoftware/`)
- FastQC: quality assessment of the sequencing process
- ncbi-blast: local version of the blast tool from the NCBI
- prinseq: removal of low quality sequences
- SPAdes: de novo genome / transcriptome assembler
- sratoolkit: tool developed by the NCBI to download genomic data from the SRa repository (https://www.ncbi.nlm.nih.gov/sra) 


First of all, let's create a new folder for the practice in the Desktop. Then let's move the data folder there. Yes, the virtual machine is in German. We will also learn German!

```
cd Schreibtisch/
mkdir Assemblly
mv ~/Downloads/MBATC_Assembly/Data Assembly
cd Assembly
```


## How do NGS files look like? ##

As you can see, the files in `Data` are compressed. Let's have a look at the uncompressed file, which is in the FASTQ format.

```
cd /home/demo-user/Schreibtisch/Data
ls
gunzip ventricosus_1.fq.gz
head ventricosus_1.fq		# Remember the head command?
```

We have printed the first 10 lines of the file. The FASTQ format is similar to BLAST but each sequence has 4 lines: header (starting with "@"), nucleotide sequence, separator (usually marked with a "+"), and the quality of each base (in ascii format).

We can zip the file again. Let's keep things tidy.
```
gzip ventricosus_1.fq
```


## Did the sequencing work well? ##

During a sequencing project, millions of reads are generated, which makes it impossible to check them one by one. Yet, it is important to know if the sequencing worked well and generated high quality sequences. To do so, we will use the FastQC tool:

```
cd /home/demo-user/Schreibtisch/Assemblly/
mkdir 0-FastQC
cd 0-FastQC
mkdir RNA Mitochondrial
ls
```

```
cd /home/demo-user/Schreibtisch/Assemblly/0-FastQC
/home/demo-user/demosoftware/FastQC/fastqc /home/demo-user/Schreibtisch/Data/Mitochondrial/* -o ./Mitochondrial/
ls */
```

Look at the output printed on the screen. What happened? If you look at the folder, you'll see that new folders have been generated. Search for the folder 0-FastQC/Mitochondrial in the folder system (GUI, not on the Terminal). Double click on the ".html" files. What you are looking is the quality report of the FASTQ files. 


If it looks good, we can start with the analyses.


## Quality filtering ##

It doesn't matter how well the sequencing worked, errors happen. We can use different programs to trim and filter reads based on quality (remember the 4th line in FASTQ files?). Several studies have shown that being too strict with the filter is as bad as not filtering at all, because you end up removing too much good data as well. 

This time we will use Prinseq. For some reason, it cannot work with compressed files, so we first need to decompress fastq files.

```
cd /home/demo-user/Schreibtisch/Assemblly/
mkdir 1-Prinseq
cd 1-Prinseq
mkdir RNA Mitochondrial

# Mitochondrial 
cd Mitochondrial
cp /home/demo-user/Schreibtisch/Data/Mitochondrial/ventricosus*.gz ./
gzip -d *
perl /home/demo-user/demosoftware/prinseq-lite-0.2.4/prinseq-lite.pl -fastq ./ventricosus_1.fq -fastq2 ./ventricosus_2.fq -out_good ventricosus_good -out_bad ventricosus_bad -trim_qual_left 25 -trim_qual_right 25 -min_qual_mean 20 -min_len 75
```

To save disk space, we now remove the files that are useless and compress the others

```
gzip -9 ventricosus_good_1.fastq
gzip -9 ventricosus_good_2.fastq
rm ventricosus*q
```

Now our reads are clean and ready to be used.

## Mitogenome assembly ##

In this tutorial, we have both genomic and transcriptomic data, whose particular nature needs the be taken into account by the assembly software. Can you imagine why? 

Our assembly software will be SPAdes in two different versions, for genomic and transcriptomic data (rnaspades). 


```
cd /home/demo-user/Schreibtisch/Assemblly/
mkdir 2-SPAdes
cd 2-SPAdes
mkdir Mitochondrial
cd Mitochondrial
python /home/demo-user/demosoftware/SPAdes-4.0.0-Linux/bin/spades.py -1 ../../1-Prinseq/Mitochondrial/ventricosus_good_1.fastq.gz -2 ../../1-Prinseq/Mitochondrial/ventricosus_good_2.fastq.gz -o ./
```

Let's remove all the intermediate files.
```
rm -r as* be* corrected/ dataset.info  input_dataset.yaml  K* misc/ params.txt  pipeline_state/ run_spades* spades.log tmp warnings.log *.paths
```

Now we have the `contigs.fasta` and `scaffolds.fasta` files. There are many fasta sequences in there. How can we know which one contains mitochondrial regions?

```
head contigs.fasta
```

Exactly, we can use the software BLAST. But I can also tell you that the first sequence in `contigs.fasta` is ~15 Kb long, which is suspiciously similar to the length of a snail mitogenome.

Let's select the first sequence. A simple way of selecting the first sequence is to first convert this multi-line fasta into a single-line fasta file using "awk", and then print only the first two lines:

```
awk '/^>/ {printf("\n%s\n",$0);next; } { printf("%s",$0);}  END {printf("\n");}' < contigs.fasta outfile; mv outfile contigs.fasta
head -n 3 contigs.fasta > contig1.fa
```

Copy the sequence and blast it online. Is it what you expected?


# Mitogenome annotation

We will use GeSeq [https://chlorobox.mpimp-golm.mpg.de/geseq.html]. Upload your file, select the appropriate settings and run it! Easy :-)



# Transcriptome assembly: read quality check, filtering, assembly & BLAST

Now let's repeat every step but with transcriptome-derived reads.

Quality check with FastQC:
```
cd /home/demo-user/Schreibtisch/Assemblly/1-Prinseq/RNA/
/home/demo-user/demosoftware/FastQC/fastqc /home/demo-user/Schreibtisch/Data/RNA/* -o ./RNA
```

Filtering and trimming reads by quality with Prinseq:

```
cd /home/demo-user/Schreibtisch/Assemblly/1-Prinseq/
cp /home/tecnicasmoleculares/Escritorio/Data/RNA/* ./
gzip -d ventricosus*.gz
perl /home/demo-user/demosoftware/prinseq-lite-0.2.4/prinseq-lite.pl -fastq ./ventricosus.al.1.fastq -fastq2 ./ventricosus.al.2.fastq -out_good ventricosus_good -out_bad ventricosus_bad -trim_qual_left 25 -trim_qual_right 25 -min_qual_mean 20 -min_len 75

gzip -9 ventricosus_good_1.fastq
gzip -9 ventricosus_good_2.fastq
rm ventricosus*q

gzip -d ermineus*.gz
perl /home/demo-user/demosoftware/prinseq-lite-0.2.4/prinseq-lite.pl -fastq ./ermineus.al.1.fastq -fastq2 ./ermineus.al.2.fastq -out_good ermineus_good -out_bad ermineus_bad -trim_qual_left 25 -trim_qual_right 25 -min_qual_mean 20 -min_len 75

gzip -9 ermineus_good_1.fastq
gzip -9 ermineus_good_2.fastq
rm ermineus*q
```

Transcriptome assembly. In this case we use the SPAdes version adapted to transcriptome assembly `rnaspades`:

```
cd /home/demo-user/Schreibtisch/Assemblly/2-SPAdes/RNA
mkdir ermineus ventricosus
cd ventricosus
python /home/demo-user/demosoftware/SPAdes-4.0.0-Linux/bin/rnaspades.py -1 ../../../1-Prinseq/RNA/ventricosus_good_1.fastq.gz -2 ../../../1-Prinseq/RNA/ventricosus_good_2.fastq.gz -o ./

# Remove intermediate files to save some space

rm -r as* be* dataset.info  hard_filtered_transcripts.fasta  input_dataset.yaml K* misc/ p* r* s* tmp/ transcripts.paths

# Remove newlines from within the sequences so that they all occupy a single line.
awk '/^>/ {printf("\n%s\n",$0);next; } { printf("%s",$0);}  END {printf("\n");}' < transcripts.fasta > outfile; mv outfile transcripts.fasta
```

How long are the seuqences now? Why? Choose one sequence at random and BLAST it against NT on the web.

We can also assemble the transcriptome of *Chelyconus ermineus* following the same steps:

```
cd ../ermineus
python /home/demo-user/demosoftware/SPAdes-4.0.0-Linux/bin/rnaspades.py -1 ../../../1-Prinseq/RNA/ermineus_good_1.fastq.gz -2 ../../../1-Prinseq/RNA/ermineus_good_2.fastq.gz -o ./

# Remove intermediate files to save some space:

# Remove newlines from within the sequences so that they all occupy a single line.
awk '/^>/ {printf("\n%s\n",$0);next; } { printf("%s",$0);}  END {printf("\n");}' < transcripts.fasta > outfile; mv outfile transcripts.fasta
```

How many seuquences do you your three assemblies have? Why? 
```
grep -c ">" /home/demo-user/Schreibtisch/Assemblly/2-SPAdes/Mitochonrial/ventricosus/contigs.fasta
grep -c ">" /home/demo-user/Schreibtisch/Assemblly/2-SPAdes/RNA/ventricosus/transcripts.fasta
grep -c ">" /home/demo-user/Schreibtisch/Assemblly/2-SPAdes/RNA/ermineus/transcripts.fasta
```

As you can see, there are striking differences between the genomes and transcriptomes, even if analysed with (kind of) the same tools.

How would you know to which genomic regions each of them correspond? Running BLAST one by one on the web does not seem like the best idea. So, what?

## BLAST ##

Exactly, we can run BLAST with the command line. In this particular case, we will search for mitochondrial genes in the transcriptomes. 


Let's move back to the "Practice" folder and create a new Blast folder where to run this analysis:

```
cd /home/demo-user/Schreibtisch/Assemblly/
mkdir 3-Blast
cd 3-Blast
```

The first thing we need is a database. You can make your own database, depending on the genes you are looking for. You can also download the entire NCBI database, but that would take too long and it need more than 200Gb of space. We have created a custom database containing all the mitochondrial genes published for cone snails (back in 2017, there might be more now). You can find it in "Data". Copy it to our current directory.

```
cp ../../Data/db_mtDNA_genes.fasta .
head ./db_mtDNA_genes.fasta
```

This is just a fasta file, as you can see. BLAST needs to index the file.

```
/home/demo-user/demosoftware/ncbi-blast-2.16+/bin/makeblastdb -in db_mtDNA_genes.fasta -dbtype nucl -out db_mtDNA_genes.fasta
ls
```

BLAST has created three new files, using as base the name we provided under the "-out" parameter.


Lets now run BLAST searches for each of the transcriptomes:

```
/home/demo-user/demosoftware/ncbi-blast-2.16+/bin/blastn -query ../2-SPAdes/RNA/ventricosus/transcripts.fa -db ./db_mtDNA_genes.fasta -outfmt 6 -evalue 1e-1 -max_target_seqs 3 -out ventricosus_blast.txt
less -S ventricosus_blast.txt

/home/demo-user/demosoftware/ncbi-blast-2.16+/bin/blastn -query ../2-SPAdes/RNA/ermineus/transcripts.fa -db ./db_mtDNA_genes.fasta -outfmt 6 -evalue 1e-1 -max_target_seqs 3 -out ermineus_blast.txt
less -S ermineus_blast.txt
```

The output files of the BLAST search are simple tables, summarising the main hits for each one of your transcripts.

## Cogratulations!

You have reached the end of this Tutorial. You have familiarized yourself with the command line, learn the structure of some basic bioinformatic file formats and commonly used tools to evaluate and filter raw sequencing data, as well as to assemble genomic and transcriptomic reads, and to search for homologous sequences with BLAST. Not bad! :-D

## Going beyond this Tutorial, just for the brave!

If you were fast in finishing this Tutorial you can move forward with your assemblies and for example, infer a phylogenetic tree from concatenated mitochondrial genes. If you reached this, it means you are already an expert in the command line, and so you'll need to figure out the exact commands to run each program (we can help!).

Briefly: Extract 2 or 3 mitochondrial genes from your newly assembled mitochondrial genome. Download homlogs for these two genes for a few more snail species (tip: use species with fully sequenced mitochondrial genomes, since you will need several mitochondrial sequences for each of the species. Create individual locus files, align them with MAFFT, concatenate the alignments with FASConCAT [https://github.com/PatrickKueck/FASconCAT], and perform a concatenated maximum likelihood analysis with IQ-TREE.
