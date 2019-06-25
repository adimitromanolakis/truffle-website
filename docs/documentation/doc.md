
---
title: "TRUFFLE – v1.2"
author: "Apostolos Dimitromanolakis"
date: "`r Sys.Date()`"

output: rmarkdown::html_vignette

vignette: >   
  %\VignetteIndexEntry{Quickstart2}
  %\VignetteEngine{knitr::rmarkdown}
  %\VignetteEncoding{UTF-8}

---

```{r,results='asis',echo=F}
cat("<link href='https://fonts.googleapis.com/css?family=Nunito' rel='stylesheet'><style>body { zoom: 1.4; }  
h2,h3,h4,h5 { font-family: Lato; } 

h1 { font-family: Oswald; font-weight:400; margin-top:50px; } 
h2 { font-size: 130%; }
h3 { font-size: 110%; }
h4 { font-size: 105%; font-family:Courier; white-space:pre; }
h5 { font-size: 100%; }


p,li { font-family: Nunito; }

.main-content pre> code {  awhite-space: pre-wr
ap; } 

pre,code { white-space: pre;  }

x#truffle\\.ibd { background:red; }
#truffle\\.segments > .sourceCode { font-size:90%;     background-color: #f7f7f7; }

#truffle\\.ibd > .sourceCode { font-size:90%;     background-color: #f7f7f7; }
#truffle\\.segments > code { background:blue; overflow:scroll; }
</style>")



```


Fast shared segment and ancestry estimation. 


# Quickstart


## Introduction 
Estimating relatedness and co-ancestry among pairs of individuals is a commonly encountered task in most association or 
population genetics studies. TRUFFLE enables the simple and accurate identification of IBD1 and 2 segments, 
calculation of total IBD1/2 probabilities and provide graphical reporting of distribution of shared segments 
across pairs of individuals. 


## Important Notes for this release

Truffle is currently under development. There might be serious bugs and issues that we are currently working on. 
If you use this version of truffle, we ask you to notify us of any problems or errors encountered. If truffle works perfectly, we
would also like your feedback.


## Input file requirements

Truffle requires a single *whole-genome* vcf input file with genotypes for all samples. 

In general any vcf file will work as long as it contains information from all the autosomal chromosomes. If you have a plink bed/bim file that you previously ld-pruned and filtered, truffle can use it as long as you convert it back to vcf using the plink command `--recode-vcf`.

## Number of marker requirements

For this pre-release version we recommend filtering down the markers of the vcf file to 60-200k. 

If you have an unfiltered vcf file with < 10 million markers, truffle has embedded functionality for filtering the vcf file before analysis.

## Quickstart


VCF file with 60-200k markers:

```sh
./truffle --vcf input.vcf.gz --cpu 4
```


Unfiltered VCF file, with millions of markers:

```sh
./truffle --vcf input.vcf.gz --cpu 4 --mindist 2000 --maf 0.1
```


Also generate a list of segments identified:

```sh
./truffle --vcf input.vcf.gz --cpu 4 --segments
```


## Example data

You can download a VCF file used for the examples below from:

[fs-and-po-pairs-from-1000genomes.vcf.gz 4.5MB](https://adimitromanolakis.github.io/truffle/fs-and-po-pairs-from-1000genomes.vcf.gz)

This dataset includes 47 selected CEU samples from 1000 genomes sequencing data, including some parent-offpring and full-sibling pairs.



## Other types of analysis

Truffle can read most common vcf files as input to the segment detection algorithm. The file should, generally, contain
all chromosomes from all individuals. Currently, there is no support for joining vcf files from different chromosomes.

For most common use cases, the vcf file should contain autosomal chromosomes only.

## Running a single chromosome

If you would like to perform segment identification on a single chromosome, the sensitivity must bu adjusted by the corresponding length of the chromosome divided by the total genome length. As a starting point we recommend a setting of `--L 10`.

## Output a list of segments

To generate a list of segments in the file truffle.segments use the option `--segments`:

```sh
./truffle --vcf input.vcf.gz --cpu 4 ---segments
```


# Output files description

## truffle.ibd

An example output file is as follows:

```sh
           ID1                ID2    NMARK  NCOMMON         IBD0        IBD1_MAX IBD1_NSEGS      IBD1        IBD2_MAX IBD2_NSEGS      IBD2    
    P7_HG02657         P5_HG02085   169413        0     1.000000             430          0  0.000000              38          0  0.000000    
    P7_HG02658         P1_HG00269   169413        0     1.000000             456          0  0.000000              40          0  0.000000    
    P7_HG02658         P6_HG02429   169413        0     1.000000             225          0  0.000000              29          0  0.000000    
    P8_HG03343         P1_HG00269   169413        0     1.000000             233          0  0.000000              28          0  0.000000    
    P8_HG03343         P6_HG02429   169413        0     1.000000             464          0  0.000000              39          0  0.000000    
   P10_HG03754        P10_HG03750   169413        0     0.001765           13475         23  0.987498             270         18  0.010737    
   P14_NA19334        P14_NA19331   169413        0     0.263604            9753         47  0.498734            1102        115  0.237662    

```

The output file columns are:

* ID1,ID2  :  ID's of pair.

* NMARK : number of markers that were used.

* NCOMMON : not used.

* IBD0,IBD1,IBD2 : The computed proportion of genome that is IBD0,1 or 2.


* IBD1_MAX : The maximum length of segment found to be IBD1 or 2.
* IBD2_MAX : The maximum length of segment found to be IBD2.

* IBD1_NSEGS : Number of segments that are identified as IBD1 or 2.

* IBD2_NSEGS : Number of segments that are identified as 2.


In the above output file we find that the 5 first pairs are unrelated (IBD1,2 < 0.001).

The pair  P10_HG03754        P10_HG03750 is identified as a parent offspring pair (IBD1 > 0.95).

The last pair is definetely a full-sibling, having IBD1 > 0.4, IBD2 > 0.15.





## truffle.segments



```sh
TYPE              ID1             ID2  CHROM  VARSTART   VAREND              POS Mbp      LENGTH Mbp    NMARKERS     
IBD1       P0_HG00120      P0_HG00116      6     59059    59814           0.2765 Mbp      7.4560 Mbp         756
IBD2       P0_HG00120      P0_HG00116      6     64814    64872         104.6593 Mbp      1.3802 Mbp          59
IBD2       P0_HG00120      P0_HG00116      6     65197    65264         110.7780 Mbp      1.7072 Mbp          68
IBD1       P0_HG00120      P0_HG00116      6     62715    65328          52.5659 Mbp     61.1437 Mbp        2614
IBD2       P0_HG00120      P0_HG00116      6     66651    66706         139.8456 Mbp      1.8712 Mbp          56
IBD1       P0_HG00120      P0_HG00116      6     66064    66818         130.3246 Mbp     13.5776 Mbp         755



```

The output file columns are:

* ID1,ID2  :  ID's of pair.

* TYPE : whether the segment was IBD1 or 2.

* CHROM :  chromosome name.

* VARSTART : the index of marker on the start of the segment. This corresponds to the n'th marker in the VCF file (if filtering is applied this might not be the same as the n'th marker
in the original vcf file

* VAREND : the index of marker at the end of the segment.

* POS : Position of the start of the segment in MBpairs.

* LENGTH : Length of the segment in MBp.

* NMARKERS  : The length of the segment in number of markers.



# Notes for using truffle


## Segment detection sensitivity

Truffle has some build-in measures to adjust the sensitivity of the segments detection.

The default sensitivity threshold should produce reasonable but maybe not optimal, results for whole-genome autosomal marker panels. In many cases you might need to adjust it, for example when:

* running exome sequencing data,
* analyzing studies with mixed ethnicity individuals,
* analyzing single chromosomes.

In most cases, you get more than expected relatedness and you might want to consider adjusting the parameter L to 1.5-3. For example:

```sh
./truffle --vcf input.vcf.gz --L 2
```



### OPTIONAL: LD-pruning and filtering steps for genotyping array data

The following commands show a suggested pipeline for performing LD-pruning of variants and minor allele frequency pruning before running truffle:

```
plink --bfile input --maf 0.05 --mind 0.2 --make-bed --out /tmp/t
plink --bfile /tmp/t --maf 0.05 --geno 0.05 --make-bed --out /tmp/t

plink --bfile /tmp/t --indep-pairwise 2000 100 0.5 --out /tmp/t
cat /tmp/t.bim|awk '$1<=22' >/tmp/autosom.txt
plink --bfile /tmp/t --extract /tmp/t.prune.in --make-bed --out /tmp/t2

plink --bfile /tmp/t2 --extract /tmp/autosom.txt --recode-vcf --out /tmp/filtered
```






# Commonly used command line options

### Input/Output control


```
--vcf `vcffile
```

Reads the input `vcffile` for processing.

```
--segments
```
Output a list of identified segments in the file truffle.segments

```
--out X
```

Change the name of the output files. The results will be stored in the files X.ibd and X.segments

### CPU usage control


* `--cpu N `

Use N cpus when running truffle.


### Segment length threshold

* `--L X`

Adjust the sensitivity threshold for detecting segments to X. Default: 1. Recommended : 0.5-2 for whole genome scans. 5-22 for single chromosome scans.

For more specific marker length detection adjustments:


* `--ibs1markers n`

Set the minimum segment length to report to n consecutive markers, for IBS1 segments.

* `--ibs2markers n`

Set the minimum segment length to n consecutive markers, for IBS2 segments. This is generally 2-3 time smaller that ibs1markers.



### Variant filtering

* `--maf *X* `

Remove variants that have a minor allele frequency less than X. For example:
`--maf 0.05`

* `--missing X `

Excludes variants with missing rate more than X. For example `--missing 0.02`

* `--mindist X`

Removes variants that are less than X base-pairs apart.For example `--mindist 2000`


### Other options

* `--pair ID1 ID2`
* `--thin x`



 
