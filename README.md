# SCSA: cell type annotation for single-cell RNA-seq data
Currently most methods take manual strategies to annotate cell types after clustering the single-cell RNA-seq data. Such methods are labor-intensive and heavily rely on user expertise, which may lead to inconsistent results. We present SCSA, an automatic tool to annotate cell types from single-cell RNA-seq data, based on a score annotation model combining differentially expressed genes and confidence levels of cell markers in databases. Evaluation on real scRNA-seq datasets that SCSA is able to assign the cells into the correct types at a fully automated mode with a desirable precision.<br><br>
SCSA is maintained by Yinghao Cao [yhcao@ibms.pumc.edu.cn].
## Download and Installation
```
git clone https://github.com/bioinfo-ibms-pumc/SCSA.git
pip install pandas numpy scipy
```
## Command Lines

```  
SCSA.py [-h] -i INPUT [-o OUTPUT] [-d DB] [-s SOURCE] [-c CLUSTER]
                 [-M MARKERDB] [-f FOLDCHANGE] [-p PVALUE] [-w WEIGHT]
                 [-g SPECIES] [-k TISSUE] [-m OUTFMT] [-T CELLTYPE]
                 [-t TARGET] [-E] [-N] [-b] [-l]

optional arguments:
  -h, --help            show this help message and exit
  -i INPUT, --input INPUT
                        Input file for marker annotation.
  -o OUTPUT, --output OUTPUT
                        Output file for marker annotation.
  -d DB, --db DB        Database for annotation. (whole.db)
  -s SOURCE, --source SOURCE
                        Source of marker genes. (cellranger,[seurat])
  -c CLUSTER, --cluster CLUSTER
                        Only deal with one cluster of marker genes.
                        (all,[1],[1,2,3],[...])
  -M MARKERDB, --MarkerDB MARKERDB
                        User-defined marker database in table format with two
                        columns.First column as Cellname, Second refers to
                        Genename.
  -f FOLDCHANGE, --foldchange FOLDCHANGE
                        Fold change threshold for marker filtering. (2.0)
  -p PVALUE, --pvalue PVALUE
                        P-value threshold for marker filtering. (0.05)
  -w WEIGHT, --weight WEIGHT
                        Weight threshold for marker filtering from cellranger
                        v1.0 results. (100)
  -g SPECIES, --species SPECIES
                        Species for annotation. Only used for cellmarker
                        database. ('Human',['Mouse'])
  -k TISSUE, --tissue TISSUE
                        Tissue for annotation. Only used for cellmarker
                        database. Run '-l' option to see all tissues.
                        ('All',[...])
  -m OUTFMT, --outfmt OUTFMT
                        Output file format for marker annotation. (ms-
                        excel,[txt])
  -T CELLTYPE, --celltype CELLTYPE
                        Cell type for annotation. (normal,[cancer])
  -t TARGET, --target TARGET
                        Target to annotation class in Database.
                        (cellmarker,[cancersea])
  -E, --Gensymbol       Using gene symbol ID instead of ensembl ID in input
                        file for calculation.
  -N, --norefdb         Only using user-defined marker database for
                        annotation.
  -b, --noprint         Do not print any detail results.
  -l, --list_tissue     List tissue names in database.
  
```
## Examples
1. To annotate a human scRNA-seq sets generated by [CellRanger](https://support.10xgenomics.com/single-cell-gene-expression/software/overview/welcome), use the following code
```
  python3 SCSA.py -d whole.db -i cellranger_pbmc_3k.csv -k All -g Human -p 0.01 -f 1.5 -m txt -o sc.txt
```
2. To annotate a human scRNA-seq sets generated by 'FindAllMarkers' function of [Seurat(Butler, A., et al. Nature Biotechnology. 2018)](https://www.nature.com/articles/nbt.4096) with ensemblIDs, use the following code
```
  python3 SCSA.py -d whole.db -s seurat -i seurat_GSE72056.csv -k All -E -g Human -p 0.01 -f 1.5
```
3. To annotate a human scRNA-seq sets generated by 'FindAllMarkers' function of [Seurat(Butler, A., et al. Nature Biotechnology. 2018)](https://www.nature.com/articles/nbt.4096) with both user-defined database and CellMarker database, use the following code
```
  python3 SCSA.py -d whole.db -i seurat_GSE72056.csv -s seurat -E -f1.5 -p 0.01 -o $i.res -m txt -M user.table 
```
4. To annotate a human scRNA-seq sets generated by [CellRanger](https://support.10xgenomics.com/single-cell-gene-expression/software/overview/welcome) only with user-defined database without any detail print, use the following code
```
  python3 SCSA.py -d whole.db -i cellranger_pbmc_3k.csv -f1.5 -p 0.01 -m txt -M user.table -N -b
```
5. To annotate cluster1 of mouse scRNA-seq sets and 
To annotate cluster1 of mouse scRNA-seq sets generated by [CellRanger](https://support.10xgenomics.com/single-cell-gene-expression/software/overview/welcome), use the following code
```
  python3 SCSA.py -d whole.db -s seurat -i seurat_mouse.csv -k All -E -g Mouse -p 0.01 -f 1 -m txt -o testout -c 1
```
6. To list tissue names in the SCSA annotation database, use the following code
```
  python3 SCSA.py -i none -d whole.db -l
```
## Output explanation
The output information from stdout consists of five parts: "#Cluster","Type","Celltype","Score","Times"
```
“#Cluster” : The cluster id from input file.
```
```
“Type” : A subjective symbol for the prediction results.

   “Good” means one of the following conditions:
       1.Only one celltype found
       2.The score of the first predicted celltype is more than twice as much 
         as the second predicted celltype.
       3.The score of the second predicted celltype is a minus.

   “?” means the score of the first predicted celltype is less than twice as much
       as the second predicted celltype.

   “E” means no celltype found.
```
```
“Celltype”: The predicted celltype name.
```
```
“Score” : The predicted score for a celltype normalized by Z-score method. 
          “nan” will be assigned if only one celltype found. 
```
```
“Times” : The score of the first predicted celltype / The score of the second predicted celltype
```
---

If you use SCSA for your research, please kindly cite the following paper:

"SCSA: a cell type annotation tool for single-cell RNA-seq data".Yinghao Cao, Xiaoyue Wang, Gongxin Peng.

bioRxiv 2019.12.22.886481; doi: [https://doi.org/10.1101/2019.12.22.886481](https://doi.org/10.1101/2019.12.22.886481)
