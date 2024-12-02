# 312NP
1.NCBI and BLAST

IN Lab 3

Create a working directory-
mkdir ~/lab03-$MYGIT/312NP

Change directory[cd ~/lab03-$MYGIT/312NP]

Download sequence for NP_849193.1
【ncbi-acc-download -F fasta -m protein "NP_849193.1"】

BLAST search for STT3 family gene 
【blastp -db ../allprotein.fas -query NP_849193.1.fa -outfmt 0 -max_hsps 1 -out 312NP.blastp.typical.out】

[blastp -db ../allprotein.fas -query NP_849193.1.fa  -outfmt "6 sseqid pident length mismatch gapopen evalue bitscore pident stitle"  -max_hsps 1 -out 312NP.blastp.detail.out]

This command avoids the need to manually calculate:[grep -c H.sapiens 312NP.blastp.detail.out]

By adjusting the e value, the screened homologs will have relatively high sequence identity and high comparison scores:[awk '{if ($6< 1e-30)print $1 }' 312NP.blastp.detail.out > 312NP.blastp.detail.filtered.out]

Finding paralogs for each species:[grep -o -E "^[A-Z]\.[a-z]+" globins.blastp.detail.filtered.out  | sort | uniq -c]

The reason is 

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



