# 312NP
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








