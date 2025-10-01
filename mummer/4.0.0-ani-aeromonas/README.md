# Aeromonas ANI  

Main tool:  mummer

Additional tools:   
  -  `mash`  v2.3  
  - `ani-m.pl` v0.1 from https://github.com/lskatz/ani-m  

This calls Lee's ani-m.pl script and compare query genome against reference genome   


This docker immage contains Aeromonas ANI database, which composed of 26 aeromonas sequences downloaded from NCBI database. The downloaded contain complete and scaffold level genomes.   

This script will automatically use `mash` as quick check to see relatedness between genomes. If the two genomes have less than 0.9 mash distance, then the ANI calculation will be skipped.  

The `--symmetric` flag will run ANI comparison on both:  
  1. the query vs the reference genome, followed by...  
  2. the reference versus the query genome.  

ANI values will likely be nearly identical between the two comparisons, but differences may occur in the percentAligned or percent bases aligned value depending on the sequences present in the genome.  

```
ani-m.pl --symmetric --mash-filter 0.9 assembly.fasta /DB/*.fasta | tee output.tsv  
``` 


Better documentation for Mummer can be found at `https://github.com/mummer4/mummer`

A tutorial can be found at `https://mummer4.github.io/tutorial/tutorial.html`

And the manual can be found at `http://mummer.sourceforge.net/manual/`  


## Docker Hub:  
```
docker pull neranjan007/mummer:4.0.0-ANI-aeromonas
```  
