# Processing of raw 16S rRNA gene reads with UPARSE

```
## prep reads for UPARSE with J. Leff's tool
git clone https://github.com/leffj/helper-code-for-uparse.git

python helper-code-for-uparse/prep_fastq_for_uparse_paired.py -i Esther16SAmpliconData/Run2/Undetermined_S0_L001_R1_001.fastq.gz -r Esther16SAmpliconData/Run2/Undetermined_S0_L001_R2_001.fastq.gz -b Esther16SAmpliconData/Run2/Undetermined_S0_L001_I1_001.fastq.gz -m esther_map.txt -o esther_uparse

#rename sample
sed 's/barcodelabel/sample/g' demultiplexed_seqs_1.fq > R1.fq
sed 's/barcodelabel/sample/g' demultiplexed_seqs_2.fq > R2.fq
```

## Pull the latest release of Silva (1.32)
```
wget https://www.arb-silva.de/fileadmin/silva_databases/qiime/Silva_132_release.zip
unzip Silva_132_release.zip
```

# Pull reads through UPARSE pipeline

## Join paired-ends
```
#UPARSE 10.0.24, 64-bit
mkdir mergedfastq
./usearch64 -fastq_mergepairs esther_uparse/demultiplexed_seqs_1.fq -reverse esther_uparse/demultiplexed_seqs_2.fq -fastqout mergedfastq/merged.fq -fastq_maxdiffs 10 -fastq_merge_maxee 1.0
```

## Dereplicate sequences
```
./usearch64 -fastx_uniques mergedfastq/merged.fq -fastqout mergedfastq/uniques_combined_merged.fastq -sizeout
```

## Remove Singeltons
```
./usearch64 -sortbysize mergedfastq/uniques_combined_merged.fastq -fastqout mergedfastq/nosigs_uniques_combined_merged.fastq -minsize 2
```

## Precluster Sequences
```
./usearch64 -cluster_fast mergedfastq/nosigs_uniques_combined_merged.fastq -centroids_fastq mergedfastq/denoised_nosigs_uniques_combined_merged.fastq -id 0.9 -maxdiffs 5 -abskew 10 -sizein -sizeout -sort size
```

## Reference-based OTU picking against Silva 1.32
```
./usearch64 -usearch_global mergedfastq/denoised_nosigs_uniques_combined_merged.fastq -id 0.97 -db /mnt/home/kearnspa/SILVA_132_QIIME_release/rep_set/rep_set_16S_only/97/silva_132_97_16S.fna  -strand plus -uc mergedfastq/ref_seqs.uc -dbmatched mergedfastq/closed_reference.fasta -notmatchedfq mergedfastq/failed_closed.fq
```

## Sort by size and then de novo OTU picking on sequences that failed to hit GreenGenes
```
./usearch64 -sortbysize mergedfastq/failed_closed.fq -fastaout mergedfastq/sorted_failed_closed.fq

./usearch64 -cluster_otus mergedfastq/sorted_failed_closed.fq -minsize 2 -otus mergedfastq/denovo_otus.fasta -relabel OTU_dn_ -uparseout mergedfastq/denovo_out.up
```

## Combine the rep sets between de novo and reference-based OTU picking
```
cat mergedfastq/closed_reference.fasta mergedfastq/denovo_otus.fasta > mergedfastq/full_rep_set.fna
```

## Map rep_set back to pre-dereplicated sequences and make OTU tables
```
./usearch64 -usearch_global mergedfastq/merged.fq -db mergedfastq/full_rep_set.fna  -strand plus -id 0.97 -uc OTU_map.uc -otutabout mergedfastq/OTU_table.txt -biomout mergedfastq/OTU_jsn.biom
```

## Assign taxonomy to OTUS
```
#QIIME 1.8
