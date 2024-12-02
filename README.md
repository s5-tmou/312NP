# 312NP

The steps and commands I have tallied are from previous labs. Here are the commands and steps showing how I analyzed the STT3 family genes. The generated file will be attached.

# 1.NCBI and BLAST

In Lab 3

Create a working directory-

      mkdir ~/lab03-$MYGIT/312NP

Change directory

      cd ~/lab03-$MYGIT/312NP

Download sequence for NP_849193.1 in NCBI

      ncbi-acc-download -F fasta -m protein "NP_849193.1"

BLAST search for STT3 family gene 
      blastp -db ../allprotein.fas -query NP_849193.1.fa -outfmt 0 -max_hsps 1 -out 312NP.blastp.typical.out

      blastp -db ../allprotein.fas -query NP_849193.1.fa  -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle"  -max_hsps 1 -out 312NP.blastp.detail.out

This command avoids the need to manually calculate:

      grep -c H.sapiens 312NP.blastp.detail.out

By adjusting the e value, the screened homologs will have relatively high sequence identity and high comparison scores:

      awk '{if ($6< 1e-30)print $1 }' 312NP.blastp.detail.out > 312NP.blastp.detail.filtered.out

Finding paralogs for each species:

      grep -o -E "^[A-Z]\.[a-z]+" globins.blastp.detail.filtered.out  | sort | uniq -c

The result is 

      2 C.carcharias

      2 C.mydas

      2 D.rerio

      2 E.caballus

      2 F.catus

      3 G.aculeatus

      2 G.gallus

      2 H.sapiens

      6 S.salar

      2 S.townsendi

      4 X.laevis

# 2.multiple sequence alignment in muscle-Lab 4 

Create a working directory-

      mkdir ~/lab04-$MYGIT/312NP

Change directory

      cd ~/lab04-$MYGIT/312NP

Getting Sequences in BLAST Output Files from Lab 3 with the seqkit Command:

      seqkit grep --pattern-file ~/lab03-$MYGIT/312NP/312NP.blastp.detail.filtered.out ~/lab03-$MYGIT/allprotein.fas | seqkit grep -v -p "carpio" > ~/lab04-$MYGIT/312NP/312NP.homologs.fas

Getting files from Lab3 with the pattern-file command:

      --pattern-file ~/lab03-$MYGIT/312NP/312NP.blastp.detail.filtered.out ~/lab04-$MYGIT/312NP/312NP.homologs.fas

 Using MUSCLE to aligning multiple sequences:

      muscle -align ~/lab04-$MYGIT/312NP/312NP.homologs.fas -output ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas

view the alignment in alv：

      alv -kli  ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas | less -RS

      alv -kli --majority ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas | less -RS

R package msa and a script to make a PDF file:

       Rscript --vanilla ~/lab04-$MYGIT/plotMSA.R  ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas

To calulate the width of the alignment:

      alignbuddy  -al  ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas

calculate the length of the alignment after removing any column with gaps:

      alignbuddy -trm all  ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas | alignbuddy  -al

calculate the length of the alignment after removing invariant (completely conserved) positions:

      alignbuddy -dinv 'ambig' ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas | alignbuddy  -al
 
Next, we can use t_coffee to calculate the avgerage percent identity among all sequences in the alignment. The commond is: 

      t_coffee -other_pg seq_reformat -in ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas -output sim

# 3. Using IQ-TREE and Gotree to Draw midpoint tree-Lab 5

Create a working directory-

      mkdir ~/lab05-$MYGIT/312NP

Change directory

      cd ~/lab05-$MYGIT/312NP

Copy the file from lab 4.:

      sed 's/ /_/g'  ~/lab04-$MYGIT/312NP/312NP.homologs.al.fas | seqkit grep -v -r -p "dupelabel" >  ~/lab05-$MYGIT/312NP/312NP.homologsf.al.fas

Finding Maximum Likelihood Tree Estimates with IQ-TREE. Calculationg the best amino acid substitution model and amino acid frequencies, and estimating brach lengths. 

      iqtree -s ~/lab05-$MYGIT/312NP/312NP.homologsf.al.fas -bb 1000 -nt 2 

For STT3 genes, amino acid substitution model and amino acid frequencies are Q.plant+I+R3.

The nw_display program displays an ASCII graphic of the .iqtree file's containment tree.
      
      nw_display ~/lab05-$MYGIT/312NP/312NP.homologsf.al.fas.treefile

R scripts can be used to display the image, 0.4 is the size of the text labels, 15 is the label length.

      Rscript --vanilla ~/lab05-$MYGIT/plotUnrooted.R  ~/lab05-$MYGIT/312NP/312NP.homologsf.al.fas.treefile ~/lab05-$MYGIT/312NP/312NP.homologsf.al.fas.treefile.pdf 0.4 15

Using Gotree to create midpoint tree:

      gotree reroot midpoint -i ~/lab05-$MYGIT/312NP/312NP.homologsf.al.fas.treefile -o ~/lab05-$MYGIT/312NP/312NP.homologsf.al.mid.treefile

Using this command can look rooted tree:

      nw_order -c n ~/lab05-$MYGIT/312NP/312NP.homologsf.al.mid.treefile  | nw_display -

Using this command can change graphic:

      nw_order -c n ~/lab05-$MYGIT/312NP/312NP.homologsf.al.mid.treefile | nw_display -w 1000 -b 'opacity:0' -s  >  ~/lab05-$MYGIT/312NP/312NP.homologsf.al.mid.treefile.svg -

Change the svg image to pdf:

      convert  ~/lab05-$MYGIT/312NP/312NP.homologsf.al.mid.treefile.svg  ~/lab05-$MYGIT/312NP/312NP.homologsf.al.mid.treefile.pdf

Last, make a cladogram:

      nw_order -c n ~/lab05-$MYGIT/globins/globins.homologsf.al.mid.treefile | nw_topology - | nw_display -s  -w 1000 > ~/lab05-$MYGIT/globins/globins.homologsf.al.midCl.treefile.svg -

convert ~/lab05-$MYGIT/312NP/312NP.homologsf.al.midCl.treefile.svg ~/lab05-$MYGIT/312NP/312NP.homologsf.al.midCl.treefile.pdf

# 4.Gene and Species Tree-Lab 6

Installing python：

      mamba create -n my_python27_env python=2.7
conda activate my_python27_env
mamba install -y ete3


Create a working directory-

      mkdir ~/lab06-$MYGIT/312NP

Copy gene file from Lab 5 to Lab 6:

      cp ~/lab05-$MYGIT/312NP/312NP.homologs.al.mid.treefile ~/lab06-$MYGIT/312NP/312NP.homologs.al.mid.treefile

Reconcile the gene and species tree using Notung:

      java -jar ~/tools/Notung-3.0_24-beta/Notung-3.0_24-beta.jar -s ~/lab05-$MYGIT/species.tre -g ~/lab06-$MYGIT/312NP/312NP.homologsf.pruned.treefile --reconcile --speciestag prefix --savepng --events --outputdir ~/lab06-$MYGIT/312NP/

Using nw_display command can check species tree:

      nw_display ~/lab05-$MYGIT/species.tre

Using Notung assing species

      grep NOTUNG-SPECIES-TREE ~/lab06-$MYGIT/312NP/312NP.homologsf.pruned.treefile.rec.ntg | sed -e "s/^\[&&NOTUNG-SPECIES-TREE//" -e "s/\]/;/" | nw_display -

Generating gene trees within species:

      python2.7 ~/tools/recPhyloXML/python/NOTUNGtoRecPhyloXML.py -g ~/lab06-$MYGIT/312NO/312NP.homologsf.pruned.treefile.rec.ntg --include.species

Using thirdkind to create SVG file:

      thirdkind -Iie -D 40 -f ~/lab06-$MYGIT/312NP/312NP.homologsf.pruned.treefile.rec.ntg.xml -o  ~/lab06-$MYGIT/312NP/312NP.homologsf.pruned.treefile.rec.svg

Convert svg to PDF:

      convert  -density 150 ~/lab06-$MYGIT/312NP/312NP.homologsf.pruned.treefile.rec.svg ~/lab06-$MYGIT/312NP/312NP.homologsf.pruned.treefile.rec.pdf

# 5.Pfam domains-Lab 8

Create a working directory-

      mkdir ~/lab08-$MYGIT/312NP

Need to copy unaligned sequences from Lab 4:

      sed 's/*//' ~/lab04-$MYGIT/312NP/312NP.homologs.fas > ~/lab08-$MYGIT/312NP/312NP.homologs.fas

Run the rps blast command to get the desired result by adjusting the e value.

      rpsblast -query ~/lab08-$MYGIT/312NP/312NP.homologs.fas -db ~/data/Pfam/Pfam -out ~/lab08-$MYGIT/312NP/312NP.rps-blast.out  -outfmt "6 qseqid qlen qstart qend evalue stitle" -evalue .0000000001

Also need to copy the gene tree from Lab 5:

      cp ~/lab05-$MYGIT/312NP/312NP.homologsf.outgroupbeta.treefile ~/lab08-$MYGIT/312NP

Generate a PDF file by running the script, this pdf file will include the phylogenetic tree and the pfam domain prediction.【ggtree】 for drawing phylogenetic trees, 【drawProteins】 for drawing Pfam domains. This 2 package included by R script.

      Rscript  --vanilla ~/lab08-$MYGIT/plotTreeAndDomains.r ~/lab08-$MYGIT/312NP/312NP.homologsf.outgroupbeta.treefile ~/lab08-$MYGIT/312NP/312NP.rps-blast.out ~/lab08-$MYGIT/312NP/312NP.tree.rps.pdf

The results can be viewed with the 【less】 command

mlr --inidx --ifs "\t" --opprint  cat ~/lab08-$MYGIT/312NP/312NP.rps-blast.out | tail -n +2 | less -S

View proteins without annotations：

      cut -f 1 ~/lab08-$MYGIT/globins/globins.rps-blast.out | sort | uniq -c

View protein with most annotations:

      awk '{a=$4-$3;print $1,'\t',a;}' ~/lab08-$MYGIT/312NP/312NP.rps-blast.out |  sort  -k2nr
