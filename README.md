
###########################1. Quality control of sequencing data##########################

conda install -c bioconda nanoqc

nanoQC sample.fastq --outdir sample_nanoQC

###########################2. Filtering the sequencing read with low quality##################
conda install -c bioconda nanofilt

NanoFilt -q 10 --headcrop 50 sample.fastq > sample_HQ.fastq

###########################3. Identification of small eccDNAs#########################

conda install minimap2

git clone  https://github.com/YiZhang-lab/eccDNA_RCA_nanopore

minimap2 -d hg38.min hg38.fa

minimap2 -c -x map-ont hg38.min sample_HQ.fastq -o sample_HQ.PAF

python3 eccDNA_RCA_nanopore.py --fastq sample_HQ.fastq --paf sample_HQ.PAF --reference hg38.fa --info sample_small_eccDNA_info --seq sample_small_eccDNA_seq --var sample_small_eccDNA_var


###########################4. Filtering the small eccDNA with low confidence ####################

awk '$2>1' sample_small_eccDNA_info > sample_small_eccDNA_HF_info

cut -f 1 sample_small_eccDNA_HF_info > sample_small_eccDNA_HF_info_id

grep -nf sample_small_eccDNA_HF_info_id sample_small_eccDNA_seq > sample_small_eccDNA_HF_info_id_n

python2 extract_seq_by_id.py sample_small_eccDNA_seq sample_small_eccDNA_HF_info_id_n sample_small_eccDNA_HF_seq
