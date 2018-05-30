## Processing of raw 16S rRNA gene reads with UPARSE

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

## Pull reads through UPARSE pipeline
```
#UPARSE 10.0.24, 64-bit
#join paired-ends
./usearch64 -fastq_mergepairs esther_uparse/demultiplexed_seqs_1.fq -reverse esther_uparse/demultiplexed_seqs_2.fq -fastqout esther_uparse/merge.fq -fastq_maxdiffs 10 -fastq_merge_maxee 1.0

#dereplicate
```
