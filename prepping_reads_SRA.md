## Preparing reads for submissing to the NCBI SRA
### Demultiplex reads
```
#qiime v. 1.9

#demultiplex the reads for R1 and R2 separatley
#need to call "--store_demultiplexed_fastq"

#R2
split_libraries_fastq.py -i '/home/pattyjk/Desktop/JonahVentures_March2018_CabbageAmplicons/Run2/Undetermined_S0_L001_R2_001.fastq.gz' -b '/home/pattyjk/Desktop/JonahVentures_March2018_CabbageAmplicons/Run2/Undetermined_S0_L001_I1_001.fastq.gz' -m '/home/pattyjk/Desktop/bact16S_JV56redo_Mapping.txt' -o $HOME/qiimeR2 --store_demultiplexed_fastq --barcode_type 12

#R1
split_libraries_fastq.py -i '/home/pattyjk/Desktop/JonahVentures_March2018_CabbageAmplicons/Run2/Undetermined_S0_L001_R1_001.fastq.gz' -b '/home/pattyjk/Desktop/JonahVentures_March2018_CabbageAmplicons/Run2/Undetermined_S0_L001_I1_001.fastq.gz' -m '/home/pattyjk/Desktop/bact16S_JV56redo_Mapping.txt' -o $HOME/qiimeR1 --store_demultiplexed_fastq --barcode_type 12
```

### Split reads on a per sample basis
```
#make sure to call "--file_type=fastq"
#R1
split_sequence_file_on_sample_ids.py -i '/home/pattyjk/qiimeR1-2/seqs.fastq' -o qiimeR1/split --file_type=fastq

#R2
split_sequence_file_on_sample_ids.py -i '/home/pattyjk/qiimeR2/seqs.fastq' -o qiimeR2/split --file_type=fastq
```
