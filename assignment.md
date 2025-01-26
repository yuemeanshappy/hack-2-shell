# 1. Get the data files
Download the following data files from the internet using the curl command: `http://eaton-lab.org/pdsb/test.fastq.gz` and 
`http://eaton-lab.org/pdsb/iris-data-dirty.csv`. Use the `less` or `zless` commands to look at each file.
Describe what these commands do. Finally, use the `head` command to print the first 5 lines of the file `iris-data-dirty.csv`.

I used `curl -O` to save these two files with their original names rather than printing the entire content to my terminal.
However, when I checked the files, both files contained the following infomation:
```
<html>
<head><title>301 Moved Permanently</title></head>
<body>
<center><h1>301 Moved Permanently</h1></center>
<hr><center>nginx</center>
```
which indicated that the URLs had been redirected to another locations. Therefore, I used `-L` to tell curl to follow these new redirects and
download the files. `less` and `zless` have similar functions in checking the files but `zless` was used for compressed files. `-n` in the `head`
command could be used for specifying the number of lines to print, so I used `head -n 5` to print the first t lines of the csv file.

```bash
# downlad two files
curl -L -O http://eaton-lab.org/pdsb/test.fastq.gz
curl -L -O http://eaton-lab.org/pdsb/iris-data-dirty.csv

# look at each file
zless test.fastq.gz
less iris-data-dirty.csv

# use head to print the 5 lines of the csv file
head -n 5 iris-data-dirty.csv
```

```bash
# the 5 lines of the iris-data-dirty.csv
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
```

# 2. Clean the data
Use `grep`, `uniq`, and `sed` for this question. Check that all of the species names are spelled correctly in the file iris-data-dirty.csv. 
Also check for missing values stored as NA. Create a new file where mispelled names are replaced with the correct values, and lines with NA are excluded, 
and save it as iris-data-clean.csv. 
Use `cut`, `sort` and `uniq` to list the number of data values there are for each species in the new cleaned data file. Describe your work.

To check all of the species name, I needed to extract them first. Since all the species names are listed in the fifth column in the csv file, 
I wanted to use `cut -f5` function followed by `uniq` function to get all the unique names. Since the default delimiter in `cut` command is a tab, 
so I needed to specify `-d` as commas for csv files. `grep` can be used for searching target strings in a file, so I used `grep "NA"` to print out species (rows) 
that contained missing values. To create a new file where mispelled names are corrected and lines with NA are excluded, I needed to use `sed` to do name subsitutions and
use `grep -v` to exclude lines with NA. I would like to do both subsitutions at the same time, so I needed to add `-e` as an argument. The `s/` and `/g` in the subsitution
string stand for subsitution and do the subsitutions globally rather than for one time correspondingly. The new file is saved as `iris-data-clean.csv` by using an arrow symble.
To list the number of data values for each species, I needed to count the number of lines under each species. I used `cut -d ',' -f5` as before to extract all the species names,
used sort to sort the lines based on the alphabatical order, and finally count the number of lines under each species by using `uniq -c`.


```bash
# check species name in the csv file
cut -d ',' -f5 iris-data-dirty.csv | uniq

# get the missing values
grep "NA" iris-data-dirty.csv

# create a new file named iris-data-clean.csv where mispelled names are corrected and lines with NA are excluded
sed -e 's/Iris-setsa/Iris-setosa/g' -e 's/Iris-versicolour/Iris-versicolor/g' iris-data-dirty.csv | grep -v 'NA' > iris-data-clean.csv

# list the number of data values for each species in a new data file
cut -d ',' -f5 iris-data-clean.csv | sort | uniq -c
```

```bash
# species name in the iris-data-dirty.csv
Iris-setosa
Iris-setsa
Iris-setosa
Iris-versicolour
Iris-versicolor
Iris-virginica

# get lines with missing values
6.3,NA,4.9,1.5,Iris-versicolor
5.6,NA,4.1,1.3,Iris-versicolor

# list the number of data values for each species
50 Iris-setosa
48 Iris-versicolor
50 Iris-virginica
```

# 3. Find a sequence
Find how many lines in the data file test.fastq.gz start with "TGCAG" and end with "GAG". Describe your work.

Since this file is a compressed file, I needed to use `zgrep` instead of `grep` to find target lines that satisfy the searching patterns. I used `^TGCAG` and
`GAG$` to specify the target lines should start with "TGCAG" and end with "GAG", and used `.*` to allow for any characters in between. I used `wc -l` to count
the number of lines that satisfy these patterns.

```bash
# the number of lines in the test.fastq.gz start with "TGCAG" and end with "GAG"
zgrep '^TGCAG.*GAG$' test.fastq.gz | wc -l
```

```bash
# the number of lines in the test.fastq.gz start with "TGCAG" and end with "GAG"
44
```

# 4. Find a sequence chunk
Using grep and other tools if necessary find all lines that contain the sequence "AAAACCCC" and for each print that line, the line above it, and two lines below it 
(so that a 4-line chunk around each search hit is printed). Describe your work.

I used `zgrep` instead of `grep` to find target sequences for a compressed file. `-B 1` can print one line above it and `-A 2` can print two lines after it. 

```bash
zgrep -B 1 -A 2 "AAAACCCC" test.fastq.gz
```

```bash
@32082_przewalskii.98 GRC13_0027_FC:4:1:5669:1669 length=74
TGCAGAATAGATAGGAAACGTTTTGGCGCTGTAGACATTAAAACCCCAGTAGGACACGGGTATCACAACGTACA
+32082_przewalskii.98 GRC13_0027_FC:4:1:5669:1669 length=74
IIIIIIIIIIIIIIIIIIHIHIIIIIIIIGIIIGIIIIIIHIIIIIIIHIIIIHIIIIIIEHIHHIIIIICIHI
--
@33413_thamno.59 GRC13_0027_FC:4:1:5000:1620 length=74
TGCAGTGGATCGAAAACCCCGAGGCTCAAGGTCACGCCACCGTCTTCGTGGCCAAGTTCTTCGGCCGCGCCGGC
+33413_thamno.59 GRC13_0027_FC:4:1:5000:1620 length=74
IIIIIIIIIIIIIIIIIIIIDHIIHHIIIIIEIBGBGGGIIHEHHHIEBBHHIEGGDGIGGHAEFDBFBDDB?D
```
