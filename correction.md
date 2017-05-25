As a first step in this practical, we will explore read correction: how it works in the general case and how the unequal coverage depth of metagenomic datasets can confound it.

# General case: correcting genomic reads

Let us start by downloading from GenBank the complete genome sequences of some bacteria; I suggest _Escherichia coli_, _Bacillus subtilis_ and _Vibrio cholerae_ (which is made up of two chromosomes). Let us rename the three corresponding FASTA files as Ecoli.fasta, Bsubtilis.fasta and Vcholerae.fasta.

Use `sim_reads` (part of the IDBA_UD package) to generate e.g. 100x of 150 bp reads with 1% error (typical of Illumina sequences): `sim_reads --depth 100 --read_length 150 --error_rate 0.01 Ecoli.fasta --print_correct Ecoli_100_150_0.01.fa`.

Use `jellyfish` to plot the distribution of kmer (e.g., 21-mer) frequencies among your reads: `jellyfish count -m 21 -o Ecoli_100_150_0.01.kmers -s 100M -C Ecoli_100_150_0.01.fa` then `jellyfish histo Ecoli_100_150_0.01.kmers > Ecoli_100_150_0.01.kmers_histogram`.

Use `xmgrace` to plot the histogram: `xmgrace Ecoli_100_150_0.01.kmers_histogram 1percenterror_histo.txt`. Once you format the graph in the way you like (fonts, axis labels, etc.), you can save these parameters in a file called e.g. params, then use this formatting for other similar graphs using `xmgrace -p params ...`.

If everything went well, you should see on the graph the very clear gap between between low-frequency kmers (which are most likely due to sequencing errors) and high-frequency ones (which are most likely correct). If you try to simulate other read datasets with higher error rates and plot the kmer distribution, you will see that this large gap becomes smaller when the error rate increases, making it very difficult to find a clear gap in the distribution if the error rate is higher than 10% (which is why PacBio and Nanopore reads are notoriously difficult to correct _de novo_). You will remark that the kmer frequency distribution is shifted to the left compared to the expected base coverage depth distribution: any idea why this is the case? Can you infer the average base coverage depth from the peak in kmer frequency?

One simple way to "correct" reads would be to discard all low-frequency kmers and keep only high-frequency ones, but this would make holes in the reads: to avoid such holes, a more sophisticated (but slightly more error-prone) approach is to attempt to correct the reads by flagging low-frequency kmers as "non-genomic" and trying to replace them with highly similar "genomic" (i.e., high-frequency) ones. You can try it for yourself using Bloocoo on the read datasets you simulated: `Bloocoo -file Ecoli_100_150_0.01.fa -kmer-size 27  -abundance auto`. Use jellyfish to calculate the distribution of kmer frequencies in the corrected dataset, then use xmgrace to plot together the distributions in the original and corrected datasets. What do you see?

To find out whether Bloocoo did a perfect job of correcting the errors, you can compare the corrected reads with the 'true reads' (which were saved by sim_reads) using the bash command `diff`: `diff -y --suppress-common-lines Ecoli_100_150_0.01.fa.corrected Ecoli_100_150_0.01.fa.correct`.

# One step higher: correcting metagenomic reads

Let us simulate a metagenomic dataset by generating e.g. 100x coverage of _E.coli_ reads, 40x coverage of _B. subtilis_ reads and 10x coverage of _V. cholerae_ reads using `simreads` then `cat` (to concatenate the three datasets).

Calculate and plot the distribution of 21-mers frequencies in the resulting dataset. What can you say?

Try to get `Bloocoo` to correct the data. Use `jellyfish` to calculate the distribution of kmer frequencies in the corrected dataset, then use `xmgrace` to plot together the distributions in the original and corrected datasets. What do you see? You can also count the number of errors in the corrected data by comparing them to the "true" reads using `diff`.


