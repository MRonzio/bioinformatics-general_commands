# Command-line utilities in Bioinformatics

Since the begin of my thesis internship I discovered and applied several different commands in order to get done some tasks in bioinformatics. Now I'm a PhD student and still I'm learning some new fancy ways to execute bioinformatics tasks. Here are some of the commands I've been using. The page is constantly being updated.
I added also some common configurations of commands in NGS analysis.

## OS

All these command were executed on Linux machines (mainly Ubuntu and CentOS).

## Bash

Show text file from line number 2.

```bash
tail --lines=+2
```

Sort numerically column 2.

```bash
sort -n -k2
```

Print last N rows.

```bash
tail -n N # or tail -N
```

Check the presence of files in a directory from a file list.

```bash
while IFS= read -r f; do if [[ -e path/$f ]]; then printf '%s exists in %s\n' "$f" "path/";else printf '%s is missing in %s\n' "$f" path/;fi; done < listfile
```
```bash

Copy all bed files that have row number higher than 10000 to another folder.

for f in *.bed; do lines=$(cat "$f" | wc -l); if [ $lines -gt 10000 ] ; then echo "moving $f with $lines lines to path/"; cp $f path/ ; fi; done | wc -l

convert file to upper case, reconvert CHR to lovercase

tr [:lower:] [:upper:] < input.file > output.file && sed -i -e 's/CHR/chr/g' output.file
```

## Awk
Add a file2 column to a file1 if a there is matching of another file2 column. $2 is the comparator of both. $4 is the line to be added from file1.

```bash
awk 'NR==FNR {stage[$2]=$4; next;}; {print $0,stage[$2]}' file1 file2
```
Split by regex (#Term).

```bash
awk '/^#Term/{g++} { print $0 > FILENAME"_split-"g".txt"}' file
```

Find and print duplicates based on column value.

```bash
awk -F"\t" '!seen[$1, $2]++' < file
```

Sum 11th column values when on column 5 a value is repeated. For each unique
column 5 value a corresponding 11th column sum is obtained. (see group_by + sum)

```bash
awk '{a[$5]+=$11}END{for (i in a) print i,a[i]}' file
```

## Perl

Find and print duplicate rows.

```bash
perl -ne 'print if $SEEN{$_}++' < file
```

## NGS

Convert SAM to BAM

```bash
samtools view -S -b -o outfile.bam infile.sam
```

Peak call with MACS2
genome: mm for mouse or hs for human.

```bash
macs2 callpeak -t treatment.bam -c control.bam -q 0.01 -f BAM -g genome -n outfile
```

## Pipelines

Remove multi-located annotations on RefSeq GTF.
```bash
#obtain uniq chr-NM
grep -v "hap" GTF_REFSEQ.dms | awk -F "\t" '{print $1,$9}' OFS="\t" | uniq | sort -k2 -u > GTF_REFSEQ_2.dms

#filter based on file1 matching file2
awk 'NR==FNR {c[$1,$2]++; next} c[$1,$9]' GTF_REFSEQ_2.dms GTF_REFSEQ.dms > out
```


## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

