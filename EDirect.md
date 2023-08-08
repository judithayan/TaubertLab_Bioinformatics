See here: https://www.ncbi.nlm.nih.gov/books/NBK179288/

### Install edirect on remote

Installation:
```bash
sh -c "$(curl -fsSL ftp://ftp.ncbi.nlm.nih.gov/entrez/entrezdirect/install-edirect.sh)"
```
Follow the prompts to set the $PATH variable.

Load edirect on currect session:
```bash
export PATH=${PATH}:${HOME}/edirect
```

### Query fields and output to txt

View entire entry xml:
```bash
esummary -db sra -id SRR8308157
```
Sample xml for SRR:
![[Pasted image 20220525232129.png]]

View specific fields as table: (single query)
```bash
esearch -db sra -query "SRR8308157[ACCN]" | efetch -format docsum | xtract -pattern DocumentSummary -sep "|" -element Run@acc Title Statistics@total_size Platform@instrument_model Experiment@acc Study@acc Study@name

```

View specific fields as table: (multiple query)
```bash
esummary -db sra -id SRR8308157,SRR8308158 | xtract -pattern DocumentSummary -sep "|" -element Run@acc Title Statistics@total_size Platform@instrument_model Experiment@acc Study@acc Study@name > meta.txt
```
If need to save, add `> meta.txt` at the end

Get a batch of files specified by txt:
```bash
# copy file to remote
cd directory
scp SRR_Acc_List.txt jyan@cedar.computecanada.ca:~/scratch/RNAseq/GSE123507/scripts
# efetch according to file
efetch -input "SRR_Acc_List.txt" -db sra -format docsum | xtract -pattern DocumentSummary -sep "|" -element Run@acc Title Statistics@total_size Platform@instrument_model Experiment@acc Study@acc Study@name > meta.txt
```

### Post processing
To split the title column according to delimiters
```bash
awk 'BEGIN{FS=OFS="\t"}{gsub(":|;","\t",$2)}2' meta.txt > meta_split.txt
```


