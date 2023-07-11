## samples and file lists are /project/cotton_genomics
### and genome: /90daydata/aggrandis_genome/LTS_ag100/Anthonomus_grandis_grandis/Assembly/for_NCBI_again/for_NCBI_again2/Agragra_HiC_scaffold_no_mt_singleline.fasta 
#### there are 150 kingsville (


##### run from /90daydata/aggrandis_genome/Clean_AggAgt

adapt from this 
#cd 3_pre-processing
#for fq in *_R1.fastq; do
#  echo "./gatk2_preprocess.sh /work/LAS/mhufford-lab/arnstrm/ler/0_index/ahalleri.fasta $fq $(echo $fq |sed 's/_R1/_R2/g')"
#done > process.cmds
#makeSLURMs.py 1 process.cmds
#for sub in *.sub; do
#  sbatch $sub;
#done

#test
for fq in  /project/cotton_genomics/19255Swo_N20062/xaa1/cleaned_combos_S211_R1.fastq
echo "./gatk2_preprocess.sh Agragra_HiC_scaffold_no_mt.fasta.masked $fq $(echo $fq |sed 's/_R1/_R2/g')"
done > process.cmds


for fq in /project/cotton_genomics/19255Swo_N20062/x*1/*R1*; do
echo "./gatk2_preprocess.sh Agragra_HiC_scaffold_no_mt.fasta.masked $fq $(echo $fq |sed 's/_R1/_R2/g')"; done > process.cmds

for fq in /project/cotton_genomics/18175Met_N20062/x*1/*R1*; do
echo "./gatk2_preprocess.sh Agragra_HiC_scaffold_no_mt.fasta.masked $fq $(echo $fq |sed 's/_R1/_R2/g')"; done > process.cmds

./makeSLURMs.py_new 1 process.cmds

#combine R1
for j in `cat /project/cotton_genomics/18175Met_N20062/sortu_samps18175Met.list`; do for R13 in `ls /project/cotton_genomics/18175Met_N20062/sx*/*L003*R1*`; do for R14 in `ls /project/cotton_genomics/18175Met_N20062/sx*/*L004*R1*`; do if [[ "$R13" == *"$j"_*R1* ]] && [[ "$R14" == *"$j"_*R1* ]]; then echo "cat $R13 $R14 > combo_R1_$j.fq" >> cat_r1.cmds; fi; done; done; done
./makeSLURMs.py_new 1 cat_r1.cmds

#combineR2
for j in `cat /project/cotton_genomics/18175Met_N20062/sortu_samps18175Met.list`; do for R13 in `ls /project/cotton_genomics/18175Met_N20062/sx*/*L003*R2*`; do for R14 in `ls /project/cotton_genomics/18175Met_N20062/sx*/*L004*R2*`; do if [[ "$R13" == *"$j"_*R2* ]] && [[ "$R14" == *"$j"_*R2* ]]; then echo "cat $R13 $R14 > combo_R2_$j.fq" >> cat_r2.cmds; fi; done; done; done

# change this and rerun
./makeSLURMs.py 1 cat_r2.cmds


# test blobtools:
(base) bash-5.1$ makeblastdb -in Agragra_HiC_scaffold_no_mt.fasta -dbtype nucl -parse_seqids -out Agragra_hic_noMt

blastn -query Agragra_HiC_scaffold_no_mt.fasta -db nt -outfmt '6 qseqid staxids bitscore std' -max_target_seqs 10 -max_hsps 1 -evalue 1e-25
