# BBO-Bioinformatics-Bash-Oneliner
Hi bioinformaticans and bash learner, welcome to BBO, Bioinformatics Bash Oneliner learning station. I started studying bioinformatics data three years ago, and my cs friend install Ubuntu on my lab computer. I was amazed by those single-word bash commands which are much faster than my dull scripts, so i started and insist using bash. Not all the code here is oneliner (if the ';' counts..), but i put effort on making them brief and fast.

This blog will focus on bash commands for parsing biological data, most of which are tsv files (tab-separated values); some of them are for Ubuntu system maintaining. I have been recording the bash commands on my notebook, but putting them on web will help others and myself to 'Ctrl +F '. I apologize that there won't be any citation for the codes, coz i haven't make any record of it, but they are probably from dear Google and Stackoverflow.

English and bash are not my first language, so... correct me anytime, sorry

In case you would like to check up and like my stupid questions on Stackoverflow, here's my page:
http://stackoverflow.com/users/4290753/once

Handy Bash 'oneliner' commands for tsv file editing
Symbol for search:
>> section
>function
$bash code
//comment


>> Grep
> extract text bewteen words (e.g. w1,w2)
$ grep -o -P '(?<=w1).*(?=w2)'

>grep lines without word (e.g. bbo)
$ grep -v bbo

>grep and count (e.g. bbo)
$grep -c bbo filename

>insensitive grep (e.g. bbo/BBO/Bbo)
$grep -i "bbo" filename 

>count occurrence (e.g. three times a line count three times)
$grep -o bbo filename 

>COLOR the match (e.g. bbo)!
$grep --color bbo filename 

>grep search all files in a directory(e.g. bbo)
$grep -R bbo /path/to/directory or
$grep -r bbo /path/to/directory 

>search all files in directory, only output file names with matches(e.g. bbo)
$grep -Rh bbo /path/to/directory or
$grep -rh bbo /path/to/directory 

>grep OR (e.g. A or B or C or D)
$grep 'A\|B\|C\|D' 

>grep AND (e.g. A and B)
$grep 'A.*B' 

>grep all content of a fileA from fileB
$grep -f fileA fileB 

>grep a tab
$grep $'\t' 

>>Sed
>remove lines with word (e.g. bbo)
$sed "/bbo/d" filename

>edit infile (edit and save)
$sed -i "/bbo/d" filename

>when using variable (e.g. $i), use double quotes " "
e.g. add >$i to the first line (to make a FASTA file)
$sed "1i >$i"  
//notice the double quotes! in other examples, you can use a single quote, but here, no way! 
//'1i' means insert to first line

>delete empty lines
$sed '/^\s*$/d' or
$sed 's/^$/d' 

>delete last line
$sed '$d' 

>add \n every nth character (e.g. every 4th character)
$sed 's/.\{4\}/&\n/g' 

>substitution (e.g. replace A by B)
$sed 's/A/B/g' filename 

>select lines start with string (e.g. bbo)
$sed -n '/^@S/p' 

>delete lines with string (e.g. bbo)
$sed '/bbo/d' filename 

>print every nth lines
$sed -n '0~3p' filename
//catch 0: start; 3: step

>print every odd # lines
$sed -n '1~2p' 

>print every third line including the first line
$sed -n '1p;0~3p' 

>remove leading whitespace and tabs
$sed -e 's/^[ \t]*//'
//notice a whitespace before '\t'!!

>remove only leading whitespace
$sed 's/ *//'
//notice a whitespace before '*'!!

>remove ending commas
$sed 's/,$//g' 

>add a column to the end
$sed "s/$/\t$i/"
//$i is the valuable you want to add
e.g. add the filename to every last column of the file
$for i in $(ls);do sed -i "s/$/\t$i/" $i;done

>remove newline\ nextline
$sed ':a;N;$!ba;s/\n//g'


>>Awk
>set tab as field separator
$awk -F $'\t' 

>output as tab separated (also as field separator)
$awk -v OFS='\t' 

>pass variable
a=bbo;b=obb;
awk -v a="$a" -v b="$b" "$1==a && $10=b' filename 

>print number of characters on each line
$awk '{print length ($0);}' filename 

>find number of columns
$awk '{print NF}' 

>reverse column order
$awk '{print $2, $1}' 

>check if there is a comma in a column (e.g. column $1)
$awk '$1~/,/ {print}' 

>split and do for loop
$awk '{split($2, a,",");for (i in a) print $1"\t"a[i]} filename 

>print all lines before nth occurence of a string (e.g stop print lines when bbo appears 7 times)
$awk -v N=7 '{print}/bbo/&& --N<=0 {exit}'

>add string to the beginning of a column (e.g add "chr" to column $3)
$awk 'BEGIN{OFS="\t"}$3="chr"$3' 

>remove lines with string (e.g. bbo)
$awk '!/bbo/' file 

>column subtraction
$cat file| awk -F '\t' 'BEGIN {SUM=0}{SUM+=$3-$2}END{print SUM}'

>usage and meaning of NR and FNR
e.g.
fileA:
a
b
c
fileB:
d
e
$awk 'print FILENAME, NR,FNR,$0}' fileA fileB fileA    1    1    a
fileA    2    2    b
fileA    3    3    c
fileB    4    1    d
fileB    5    2    e

>and gate
e.g.
fileA:
1    0
2    1
3    1
4    0
fileB:
1    0
2    1
3    0
4    1
$awk -v OFS='\t' 'NR=FNR{a[$1]=$2;next} NF {print $1,((a[$1]=$2)? $2:"0")}' fileA fileB 1    0
2    1
3    0
4    0

>round all numbers of file (e.g. 2 significant figure)
$awk '{while (match($0, /[0-9]+\[0-9]+/)){
\printf "%s%.2f", substr($0,0,RSTART-1),substr($0,RSTART,RLENGTH)
\$0=substr($0, RSTART+RLENGTH)
\}
\print
\}'

>give number/index to every row
$awk '{printf("%s\t%s\n",NR,$0)}'

>>Xargs
>set tab as delimiter (default:space)
$xargs -d\t

>display 3 items per line
$echo 1 2 3 4 5 6| xargs -n 3
//1 2 3
  4 5 6

>prompt before execution
$echo a b c |xargs -p -n 3

>print command along with output
$xargs -t abcd
///bin/echo abcd
//abcd

>with find and rm
$find . -name "*.html"|xargs rm -rf

>delete fiels with whitespace in filename (e.g. "hello 2001")
$find . -name "*.c" -print0|xargs -0 rm -rf

>show limits
$xargs --show-limits

>move files to folder
$find . -name "*.bak" -print 0|xargs -0 -I {} mv {} ~/old
or
$find . -name "*.bak" -print 0|xargs -0 -I file mv file ~/old

>move first 100th files to a directory (e.g. d1)
$ls |head -100|xargs -I {} mv {} d1

>parallel
$time echo {1..5} |xargs -n 1 -P 5 sleepa lot faster than
$time echo {1..5} |xargs -n1 sleep

>copy all files from A to B
$find /dir/to/A -type f -name "*.py" -print 0| xargs -0 -r -I file cp -v -p file --target-directory=/path/to/B
//v: verbose|
//p: keep detail (e.g. owner)

>with sed
$ls |xargs -n1 -I file sed -i '/^Pos/d' filename

>add the file name to the first line of file
$ls |sed 's/.txt//g'|xargs -n1 -I file sed -i -e '1 i\>file\' file.txt

>count all files
$ls |xargs -n1 wc -l

>to filter txt to a single line
$ls -l| xargs

>count files within directories
$echo mso{1..8}|xargs -n1 bash -c 'echo -n "$1:"; ls -la "$1"| grep -w 74 |wc -l' --
// "--" signals the end of options and display further option processing

>download dependencies files and install (e.g. requirements.txt)
$cat requirements.txt| xargs -n1 sudo pip install

>count lines in all file, also count total lines
$ls|xargs wc -l



>>Find 
>list all sub directory/file in the current directory
$find .

>list all files under the current directory
$find . -type f

>list all directories under the current directory
$find . -type d

>edit all files under current directory (e.g. replace 'www' with 'ww')
$find . name '*.php' -exec sed -i 's/www/w/g' {} \;
>if no subdirectory
$replace "www" "w" -- *
//a space before *

>find and output only filename (e.g. "mso")
$find mso*/ -name M* -printf "%f\n"

>find and delete file with size less than (e.g. 74 byte)
$find . -name "*.mso" -size -74c -delete
//M for MB, etc


>>Others
>remove newline / nextline
$tr --delete '\n' <input.txt >output.txt

>replace newline
$ tr '\n' ' ' <filename

>compare files (e.g. fileA, fileB)
$diff fileA fileB
//a: added; d:delete; c:changed
or
$sdiff fileA fileB
//side-to-side merge of file differences

>number a file (e.g. fileA)
$nl fileA
or
$nl -nrz fileA
//add leading zeros

>combine/ paste two files (e.g. fileA, fileB)
$paste fileA fileB
//default tab seperated

>reverse string
$echo 12345| rev

>read .gz file without extracting
$zmore filename
or
$zless filename

>run in background, output error file
$(command here) 2>log &
or
$(command here) 2>&1| tee logfile
or
$(command here) 2>&1 >>outfile
//0: standard input; 1: standard output; 2: standard error

>send mail
$echo 'heres the content'| mail -A 'file.txt' -s 'mail.subject' me@gmail.com
//use -a flag to set send from (-a "From: some@mail.tld")

>.xls to csv
$xls2csv filename

>append to file (e.g. hihi)
$echo 'hihi' >>filename

>make BEEP found
$speaker-test -t sine -f 1000 -l1
>set beep duration
$(speaker-test -t sine -f 1000) & pid=$!;sleep 0.1s;kill -9 $pid

>history edit/ delete
$~/.bash_history
or
$history -d [line_number]

>get last history/record filename
$head !$

>clean screen
$clear
or
$Ctrl+l

>send data to last edited file
$cat /directory/to/file
$echo 100>!$

>run history number (e.g. 53)
$!53

>run last command
$!!

>run last command that began with (e.g. cat filename)
$!cat
or
$!c
//run cat filename again

>extract .xf
1. $unxz filename.tar.xz
2. $tar -xf filename.tar

>install python package
$pip install packagename

>random order (lucky draw)
$for i in a b c d e; do echo $i; done| shuf

>echo a random number
$echo $RANDOM

>Download file if necessary
$data=file.txt
url=http://www.example.com/$data
if [! -s $data];then
    echo "downloading test data..."
    wget $url
fi

>wget to a filename (when a long name)
$wget -O filename "http://example.com"

>wget files to a folder
$wget -P /path/to/directory "http://example.com"

>delete current bash command
$Ctrl+U
or
$Ctrl+C
or
$Alt+Shift+#
//to make it to history

>add things to history (e.g. "addmetohistory")
$#addmetodistory
//just add a "#" before~~

>sleep awhile or wait for a moment or schedule a job
$sleep 5;echo hi

>count the time for executing a command
$time echo hi

>backup with rsync
$rsync -av filename filename.bak
$rsync -av directory directory.bak
$rsync -av --ignore_existing directory/ directory.bak
$rsync -av --update directory directory.bak
//skip files that are newer on receiver (i prefer this one!)

>make all directories at one time!
$mkdir -p project/{lib/ext,bin,src,doc/{html,info,pdf},demo/stat}
//-p: make parent directory
//this will create project/doc/html/; project/doc/info; project/lib/ext ,etc

>run command only if another command returns zero exit status (well done)
$cd tmp/ && tar xvf ~/a.tar

>run command only if another command returns non-zero exit status (not finish)
$cd tmp/a/b/c ||mkdir -p tmp/a/b/c

>extract to a path
$tar xvf -C /path/to/directory filename.gz

>use backslash "\" to break long command
$cd tmp/a/b/c \
> || \
>mkdir -p tmp/a/b/c

>get pwd
$VAR=$PWD; cd ~; tar xvf -C $VAR file.tar
//PWD need to be capital letter

>list file type of file (e.g. /tmp/)
$file /tmp/
//tmp/: directory

>bash script
#!/bin/bash
file=${1#*.}
//remove string before a "."
file=${1%.*}
//remove string after a "."








=-=-=-=-=-A lot more coming!! =-=-=-=-=-=-=-=-=-=waitwait-=-=-=-=-=-=-=-=-=-