# annotation of PPMI WGS data that matches the IPS data

## Annotating full IPS PPMI cohort
Cornelis
31 October 2019
working directory
ID = PPMI_IDS.txt
N=134
create output folder

## create vcf file per chromosome
cd $PATH
mkdir PPMI_IPS_annotation

for chnum in {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22};
do
	plink2 --pfile pd.june2019.chr$chnum.sqc --export vcf \
	--mac 1 --out PPMI_IPS_annotation/PPMI_IDS_$chnum --keep-allele-order --keep PPMI_IDS.txt
done

### note 133 in PD WGS file, PPMISI3186 missing....

## then run annovar
module load annovar
cd PPMI_IPS_annotation

### annotate file
for chnum in {1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22};
do
table_annovar.pl PPMI_IDS_$chnum.vcf $ANNOVAR_DATA/hg38 --thread 20 -buildver hg38 \
-out PPMI_IDS_annotation$chnum -remove -protocol refGene,avsnp150,clinvar_20190305,gnomad211_genome \
-operation g,f,f,f -nastring . -vcfinput -polish
done

## example extractions below
### merge annotation file and extract 
cat PPMI_IDS_annotation*.hg38_multianno.txt > genome_wide_annotation_of_PPMI_IDS.txt
head -1 PPMI_IDS_annotation4.hg38_multianno.txt > header.txt
### then manually added sample IDs from .vcf file

grep "arkinson" genome_wide_annotation_of_PPMI_IDS.txt > temp
cat header.txt temp > PD_variants_in_PPMI_IDS.txt

grep "athogenic" genome_wide_annotation_of_PPMI_IDS.txt > temp
cat header.txt temp > Pathogenic_variants_in_PPMI_IDS.txt

grep "GBA" genome_wide_annotation_of_PPMI_IDS.txt > temp
cat header.txt temp > GBA_variants_in_PPMI_IDS.txt
grep "LRRK2" genome_wide_annotation_of_PPMI_IDS.txt > temp
cat header.txt temp > LRRK2_variants_in_PPMI_IDS.txt

