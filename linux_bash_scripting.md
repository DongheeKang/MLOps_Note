# Linux bash scripting


### Contents
  * [Bash Command](#Shell)  
  * [Bach Search](#Search)  
  * [Bash scripting](#Scripting)
  * [Quesionary](#Questionary)


<br/><a name="Shell"></a>
==============================================================================================

# Bash command

### Wenn ich Hilfe brauche unbekannte Programm Name!

      $ whatis <command>         : sucht Erklaerung der Befehl                            
      $ apropos <command>        : search the whatis database for complete words with apropos
      $ whereis <command>        : zeigt Pfade zu Binaer und /oder Konfigurationsdateien
      $ which <command>          : zeigt Pfade zu ausfuehrbaren Dateien.                     
      $ man -k <command>         : Pfade der Manpages und Pfade zu Programmquellen
      $ <command> --help         : typical help

### ls

      $ ls -l 'which locate'     : list of location indicated by 'which locate'
      $ ls -l $(which passwd)    : Der Befehl in der Klammer wird zusert ausgefuehrt (which passwd) und die Ausgabe dieses Befehls (/usr/bin/passwd) wird an der selben Position in die Befehlszeile eingefuegt.

      $ ls -li    : Inode show
      $ ls -lS    : sort by Size
      $ ls -lc    : sort by last modification of file status information

      $ ls -d */  : sort by last modification of file status information

### Useful command for administration  
      $ expand        : Converts tabs to spaces
      $ unexpand      : Converts spaces to tab
      $ fmt           : a formatter for simplifying and optimizing text files
      $ nl            : numbers of lines of files
      $ wc            : counts of bytes, characers, words and lines of a file
      $ sort          : sort lines of text files alphabetically
      $ uniq          : removes consecutive duplicate lines
      $ split         : splits a file into different groups/files
      $ cut           : cut the field
      $ paste         : horizontalles cat, paste together lines on a file into vertical columns
      $ join          : horizontalles cat, prints a pair of input lines
      $ pr            : Convertsa text file for printing few page printing
      $ stat          : Status of this files who access, modify, etc
      $ file          : show info about file
      $ type (ls, echo, firefox)      : type zeigt fuer ausfuehrbare Dateien

      $ who -u        : users information
      $ dmesg         : write kernal message
      $ lspci         : display information PCI buses in the system
      $ lsusb -v      : display information USB buses in the system
      $ lsdev         : display information I/O address, IRQ/DMA channels
      $ iostat        : monitoring system input/output device load
      $ vmstat        : reports virtual memory statistics about processes, memory, paging, block I/O, traps, disks and cpu activity.
      $ mpstat -P 1   : output each available processor, 0 being the first one
      $ free -h       : total amount of physical and virtual memory
      $ w             : Wer ist momentan an System angemeldet? info.  from /var/run/utmp
      $ last          : Wer war momentan an diesem System eingemeldet?
      $ uptime        : how long in service, how many user in machine
      $ lsof /tmp     : open files and corresponding processes.
      $ sar -d        : output disk statistics
      $ fuser         : a block device mounted on that directory
      $ nice          : Prozessprioritaet
      $ renice        : Prozessprioritaet
      $ pgrep         : Wie viele Prozess fuer Kang im Lauf?
      $ pkill         : kill all process of kang?

      $ strace -p program     : Debug program to connect a running process
      $ ltrace cat /dev/null  : Debug program to check a library call tracer
      $ strings /bin/bash     : print characters, useful for reading non-text files.

### Time syncronization
    $ cat /etc/timezone
    $ tzselect                   : set time zone Europe/Berlin
    $ timedatectl set-ntp true   : NTP network time synch is enable

    $ date
    $ hwclock
    $ ntpdate pool.ntp.org       : access to standard ntp server
    $ ntpq -p                    : information about current ntpd server connection
    $ ntpdc                      : ntp diagnose with interactive mode

### Local information
    $ locale                  : Language and local setting
    $ iconv                   : iconv can use for converting between win(euc-kr) and ubuntu(utf-8)

### Background prozess
    $ bg 1
    $ fg 1
    $ nohup updatedb &
    $ screen (remote shell)

### touch to change create date
    $ touch -d tomorrow test             : 
    $ touch -d '1 day ago' test          : 
    $ touch -d '5 years ago' test        : 
    $ touch test{1..10}                  : 

    $ touch -t YYMMDDHHMM fileName       :  set the timestamp 
    $ touch -r file2 file1               :  file2 is updated with the time stamp of file1

### truncate

    $ cp original.output backup.output
    $ truncate -s 0 original.output
    $ cat backup.output > original.output

### history

    $ vi .bash_history
    $ echo $HISTOFILE

    $ history
    his> !100               : re-use command of 100 line
    his> !echo              : re-use echo command used before
    his> !?test             : fueht den letzten Befehl aus, in dem test vorkommt
    his> !!                 : re-use of last command
    his> !n	                : re-use of command in line number n
    his> ^string1^string2   : repeat the last command replacing with first occurrence of string1 with string2
    his> !xxxxx:s/$$/$PPID/ :
    his> Ctrl + r           : find contents


### date
    $ date "+DATE: %m/%d/%y%nTIME: %H:%M:%S"

### cat

    process Text Streams Using Filters
    $ cat -n  : number all line
    $ cat -b  : number non-blank line
    $ tac     : wie cat but reverse print

### head & tail

    $ head -n1                    : just first line or "head -1" is same
    $ tail -f /var/log/messages   : -f (follow) "Live" stream

### expand & unexpand

    $ expand    : wandelt Tabulatorzeichen in Leerzeichen um
    $ unexpand  : wandelt Leerzeichen in Tabstopps
    $ expand    : Converts tabs to spaces (usually by default, 1 tab = 8 spaces)
    $ unexpand  : Converts spaces to tab

### fmt
a formatter for simplifying and optimizing text files

    $ fmt -w 35 text.txt                        : formatiert Text in der angegebenen Spaltenzahl, standarmaesig 75 Zeichen breite
    $ fmt -w 35 text.txt | pr -h "Title" -2     : pr bereitet Textdateien fuer eine Druckansgabe vor. Es zeigt Zeit, Datum, Seiten

### nl
numbers of lines of files

    $ nl data1 > data2  : put number in every line

### wc
counts of bytes, characers, words and lines of a file

    $ wc -c, --bytes
    $ wc -m, --chars
    $ wc -l, --lines
    $ wc -L, --max-line-length
    $ wc -w, --words

### sort

    $ sort      : sort lines of text files alphabetically
    $ sort -n   : sortiert nach numerisschen, sort reads the number and not the value
           -o   : outfile
           -r   : reverse file
    $ sort -nr  : displays summary and sorts the result in order of largest to smallest numeric and reversal
    $ sort -u   : unique option!

### uniq

    $ uniq      : removes consecutive duplicate lines, just consecutive lines, can print still duplication existing in antoher line!
    $ uniq file : reduziert mehrere identische aufeinanderfolgende Zeilen auf eine Zeile
    $ uniq -u   : removes consecutive duplicate lines, really remove duplicates!
  

### Difference Between 'sort | uniq' and 'sort -u'
    same thing!

    $ uniq -c color 
      1 Black
      1 green
      2 red                        : consecuttive line!
      1 yellow
      1 Green
      1 red                        : this is not disappered!
    $ sort color | uniq -c
      1 Black
      1 Green
      1 green
      3 red
      1 yellow

    $ sort color | uniq | wc -l
      5
    $ sort -u color | wc -l
      5

### split

    $ split      : splits a file into different groups/files
    $ split -b 11m README README_
    $ split -l 1000 README README_
    $ cat READAME_* > README

### cut

    -d Feldtrennzeichen 
    -f Feldnummer

    $ cut -d'' -f2
    $ cut -d:  -f2
    $ cut -d:  -f1,6 /etc/passwd         
    $ cat /etc/passwd | grep user | cut -f1 -d:

### xargs
    xargs build and execute command lines from standard input
    $ find . -name '*.mp3' | xargs rm

### Simple find using internal data base
    $ locate *.sxw
    $ updatedb &

### alias
    $ unalias ls   : deactive alias
    $ builtin ls   : origianlkommando zurueck

### Hexdump
    $ hexdump /dev/sda | head -n 32 | tail -n 5 : Hexdump(16bit) important for programmer

### od

    $ od -tx(-txC) /etc/passwd

### test

    $ test -e FileName        : file exists?

### read

    $ read -p "Name :" name	  : prompt asking and get name

### shift

    $ shift 2                 : move arguments to two position left

### seq

    $ seq 1 10                : print out 1 2 3 ... 10 in vertical line

### favorite

    favorite to store your favorite commands from history
    $ favorite --add myhost 'ssh me@myhost'

### rename

    $ rename 's/d0d0/psi/' *
    $ rename 's/data/M377101/' 377101/*
    $ rename "4C" "4CB" *
    $ rename htm html *
    $ rename 's/^hospital\.php\?loc=(\d{4})$/hospital_$1/' hospital.php*


<br/><a name="Search"></a>
==============================================================================================

# Bash searching
1. find
2. regular expression
3. sed
4. tr
5. awk
6. fzf

## find

### standard
* search file and directory

      $ find / -name myfile.txt
      $ find /home -name '*.sxw'
      $ find / -name "*.log"
      $ find / -name "sambda*.rpm"
      $ find / -name "samb[a-z]*"

      $ find / -name <whatever> -exec chmod 777 {} \;
      $ find / -name <whatever> | xargs chmod 777
      $ find . -name '*.root' -exec ls -l {} \;

      $ find / -type l     : type symbolic
      $ find / -type d     : type directory
      $ find / -type f     : type regular file

      $ find . -type d -name "TMVA"                         : TMVA directory 
      $ find . -type f -empty                               : empty file
      $ find . -type f -exec grep "example" '{}' \; -print  : file containg example in a finle name 
      $ find . -type f -print | xargs grep "example"        : same as above

      $ find src ! -type d                                  : find files that are not a directory
      $ find src -name "*yaml" -o -name "*.xml"             : find yaml or xml 

* some options for displaying

      -print
      -print0      : find a file contained null character
      -path
      -empty
      -executable
      -maxdepth    : directory maximum depth level

      -ctime n : Many times this is understood as a creation time but that wrong. Ctime is change time of file stats such as ownership, what permissions it has etc.
      -mtime n : File modification time. Value of mtime is updated when content of file changes
      -atime n : File access time. Value of atime is modified when file is opened. 

      the default unit is n 24-hour periods i.e. day
      please use if you want to specifiy time units, something like below

      -atime -1h30m 
      -atime -20d

      +n  : for greater than n
      -n  : for less than n
       n  : for exactly n

* executing

      -exec ls -l {}\;
      -exec cmd {} \;　
      -exec rm -i {} ＼;

      {} will be replaced by the file/directroy name by find command
      \ is used to comment out the ";"
      ; is used to end the command

* command 'ls -l' will also provide a total number of pointers to that file

      $ find . -inum 421422 -exec ls -li {} \;
        -> 421422 -rwxr-xr-x 3 root root 47288 May 24 2008 ./mkfs.ext3
        -> 421422 -rwxr-xr-x 3 root root 47288 May 24 2008 ./mkfs.ext2
        -> 421422 -rwxr-xr-x 3 root root 47288 May 24 2008 ./mke2fs

### find: advanced
      $ find /tmp -name "*.tmp" -delete                : delete any file *.tmp 
      $ find . -name "nnode" -exec rm -rf '{}' \;      : recursively remove "node" directories

      $ find . -size -10 -exec ls -l {} \;                          : list
      $ find . -size -10 -exec rm {} \;                             : remove
      $ find set_11 -size -2k -iname '*Dg*' -exec ls -alh {} \;     : list for certain size
      $ find set_10 -size -400c -exec ls -alh {} \;                 : c is a block

      $ find . -type f -size -1000 -printf "%s:%h%f\n" | grep "par"               :  
      $ find . -type f -size -1000c | grep "pid.root"                             : 
      $ find / -type f -size -1k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }'  : 

      $ find . -name core -exec ls -l {} ＼;
      $ find . -name '*.root' -exec ls -l {} \;
      $ find . -name '*.mp3' | xargs rm
      $ find . -name "*.c"   | xargs grep foo1foo2foo3
      $ find / -name 'et*' -type d
      $ find . -name ＼*.c -atime +30 -exec ls -l {} ＼;

      $ find -wholename "*/query/*.json"            : find matching whole name example

      find all *.java files containing the word “interface”
      $ find src -name "*.java" -type f -exec grep -l interface {} \;   
      $ find src -name "*.java" -type f | xargs grep -l interface

      what should be faster? 
      $ time find src -name "*.java" -type f -exec grep -l interface {} \;        : 34.048 total
      $ time find src -name "*.java" -type f | xargs grep -l interface            :  3.300 total

### find: security and ownership
* Necessariness tools and tips for security

      $ find / -perm -u+s           : SUID bit
      $ find / -perm +4000 -type f  : list of files with SUID bit
      $ find / -perm -g+s           : GUID bit
      $ find /usr -uid 0            : owned by root

* finding by owership

      $ find / -user kang                   : ownership by kang
      $ find / -perm +4000                  : Document with SUID-bit format
      $ find . -perm 700                    : match the permission mode 70
      $ find / -size +500M | xargs ls -l    : Datei grosser als 500M
      $ find properties -size 1k            : files larger than 1 kB in a directory properties

### find: display modified date

      $ find . -name '*json*' -printf "%-25p %t\n"
      $ find . -name "*json*" | xargs -d '\n' stat -c "%-25n %y"
      $ find . -name "*json*" | xargs -d '\n' ls -ld
      $ find . -name "*json*" -exec stat -c "%-25n %y" {} \;
      $ find . -name "*json*" -exec ls -ld {} \;

### find: files that have been modified recently
* via Find

      $ find . -mtime -1                           : modified less than n days ago
      $ find /home/sports -mmin +120               : modified more than 120 mins ago file and dir
      $ find /home/sports -type f -mmin +120       : modified more than 120 mins ago file only
      $ find . -type f -mmin -120 -mmin +60        : modified between 60 min < time < 120 min

      $ find . -type f -newermt 2019-07-24                        : changed earlier than 2019-07-24
      $ find . -type f -newermt 2019-07-24 ! -newermt 2019-07-25  : modifed on 2019-07-24      

      $ find . -type f -newermt "-24 hours" 
      $ find . -type f -newermt "-10 minutes" 
      $ find . -type f -newermt "1 day ago" 
      $ find . -type f -newermt "yesterday"

* via ls  

      $ ls -lt
      $ ls -lt | grep 'Jul 27'
      $ ls -lt | grep '17:'
      $ ls -ltR

### find: delete files older than certain time

      $ find . -name "access*.log" -type f -mmin +15 -delete        : delete file older than 15 mins
      $ find . -name "access*.log" -type f -mtime +5 -delete        : delete file older than 5 days
      $ find . -name "access*log" -exec rm {} \;                    : for older find version
      $ find . -name "access*log" -exec rm -i {} \;                 : will delete with a prompt
      $ find . -delete -name file.txt                               : !!!! will delete all in the directory!!!!!!

### find: time related
      $ find /home -amin 10  : zugegriffenen Datei vor n Min  
      $ find /home -cmin 20  : geaendertene Datei
      $ find /home -mtime 1  : vor 1 mal 24 Stunden modifiziert
      $ find /home -ctime 2  : vor weniger als 2 mal 24 Stunden geaendert

      $ find . -iname "*" -daystart -mtime 1 -type f
      $ find . -iname "*" -atime 10 -type -f
      $ find . -iname "*" -atime -60 -type -f

      $ find . -mtime 7 -iname ".db"                               : To find within 7 hours with .db
      $ find ./ -type f -ls | grep '10 Sep'                        : To find file created on 10 Sep
      $ find . -type f -newermt 2007-06-07 ! -newermt 2007-06-08   : files modified on the 7-6-2007
      $ find . -type f -newerat 2008-09-29 ! -newerat 2008-09-30   : all files accessed on the 29-09-2008
      $ find . -type f -newerct 2008-09-29 ! -newerct 2008-09-30   : files which had their permission changed on the same day

### find: symbolic links to a file
* case 1 

      $ ls -lrth
      lrwxrwxrwx 1 root root 5 Feb 7 15:36 saran-l -> saran

      $ find . -lname saran                   : only matches broken symbolic links                  
      $ find . -samefile mke2fs               : true if the file is a hard link to name 'mke2fs'
      $ find . -inum 421422                   : true if the file has inode number n.

* case 2 

      $ ls -lrth
      lrwxrwxrwx 1 mogamal mogamal   28 Jun 11 16:52 filelink -> /home/mogamal/test/file1.txt
      lrwxrwxrwx 1 mogamal mogamal   28 Jun 11 16:52 dirlink -> /home/mogamal/test/dir1

      $ find -L / -samefile file1.txt                     : for each symbolic link
      $ find -L / -samefile file1.txt 2> /dev/null        : ignore permission denied or so.
       
      $ stat file1.txt                                    : find inode number 
      $ find -L / -inum 94804 2> /dev/null

      $ find / -type l                                    : now using type link
      $ find / -type l -ls 2> /dev/null | more
      $ find / -type l -ls 2> /dev/null | grep dir1


### How to find broken symlinks

      $ find / -xtype l                                          : simple way
      $ find / -xtype l -ls                                      : include more information

      $ find / -type l ! -exec test -e {} \; -print              : portable Way
            ! is negating the result of the –exec expression
            \ is used to protect the command from expansion by the shell
            -e is an option for test command which is used to check file types
            -print prints the result to the standard output

      $ find -L / -type l                         : this is bad, because -L option will make find traverse the entire /usr/share structure
      $ find -D search / -xtype l                 : so without -L one can see broken link in primary
      $ find -D search -L / -type l               : with -L search entire broken links with substructure

      $ find / -type l ! -readable                : hacky way - recommend to administrator
### find: change from htm to html within the folder /var/www/
      $ find /var/www/ -name *.html -exec chmod 500 {} \;         : need permission change
      $ find . -name '*.htm' -exec mv '{}' '{}l' \;               : now... change 

### find: display modified date of file
      $ find . -name '*json*' -printf "%-25p %t\n" 

        %p – file’s name
        %M – file’s permissions
        %Tk – file’s last modification time in a format specified by k. 

      $ find . -name "*json*" | xargs -d '\n' stat -c "%-25n %y"
      $ find . -name "*json*" -exec stat -c "%-25n %y" {} \;

      $ find . -name "*json*" | xargs -d '\n' ls -ld
      $ find . -name "*json*" -exec ls -ld {} \;

### find: tar but careful for tthe name of file!

      $ tar -czf archive.tar.gz `find . -type f`                   : don't do this, doesn’t handle files with spaces
      $ find . -type f | xargs tar -czf archive.tar.gz             : same trouble.

      $ find . -type f | xargs -d "\n" tar -czf archive.tar.gz
      $ find . -type f -print0 | tar -czf archive.tar.gz --null -T -
      $ find . \( -type f -o -name '*.c' \) -print0 | tar -czf archive.tar.gz --null -T -
      $ find . -type f > archiveFileList && tar -czf archive.tar.gz -T archiveFileList
      $ find . -type f -exec tar -czf {}.tar.gz {} \;

## Regular expression

* Regular expression

      [:blank:]   : all horizontal whitespace
      [:alnum:] 	: alle alphanumerischen Zeichen [A-Za-z0-9]
      [:alpha:] 	: alle Buchstaben [A-Za-z]
      [:blank:] 	: ein oder mehrere Leerzeichen und Tab
      [:cntrl:] 	: alle Kontrollzeichen wie z.B. <newline>
      [:digit:] 	: alle dezimalen Zahlen [0-9]
      [:graph:] 	: alle druckbaren Zeichen (ASCII 33 - 126) ohne das Leerzeichen
      [:print:] 	: alle druckbaren Zeichen
      [:lower:] 	: alle Kleinbuchstaben [a-z]
      [:upper:] 	: alle Großbuchstaben [A-Z]
      [:space:] 	: Leerzeichen und horizontales Tab
      [:xdigit:] 	: alle hexadezimalen Zahlen [0-9A-Fa-f]
     
* what is this meaning!

      ^      : start of line
      $      : end of line
      \<     : start of word
      \>     : end of word
      .      : match any single character, equivalent to '?' thus, "m.a" matches "mpa" and "mea" but not "ma" or "mppa".
      ?      : any single character. "hd?" -> (hda, hdb, hdc)

      ^[^#]  : Wenn am Zeilenanfang keine Raute # steht
      $[.]   : Wenn am Zeilenende Raut . steht
      \      : Maskierung, schuetzt das folgende Metazeichen vor der Interpretation
      june\? : Matches June or Jun (? matches zero or an instance of e)
      [a-zA-Z]                        : Match any letter
      [^0-9A-Za-z]                    : Any non alphanumeric character
      [A-Z][a-z]*                     : An uppercase character followed by zero or lower case character
      \$[ 0-9]*\.[0-9]\{2\}           : Matches $xx.xx or $ xxx.xx, whereas the chracters following $ could be a space or number
      [0-9]\{2}-[0-9]\{4\}-[0-9]\{3\} : Matches numbers on the pattern xx-xxxx-xxx

* Zeichenklassen,  Wiederholungsoperatoren

      .           ist ein Platzhalter und bezeichnet jedes einzelne Zeichen außer das Zeilenende.
      [abc$]      trifft alle aufgeführten Zeichen.
      a-c         bezeichnet alle Zeichen im angegebenen Limit.
      [^exp]      trifft alle Zeichen außer den angegebenen.
      ^abc 	      trifft das angegebene Muster, wenn es am Zeilenanfang steht.
      abc$ 	      trifft das angegebene Muster, wenn es am Zeilenende steht.
      \           Maskierung des folgenden Zeichens ($\rightarrow$2.3.2)
      .aus        trifft Haus, raus, Maus, Laus,...
      xy*z        trifft auf xy...was auch immer...z
      ^abc        jede Zeile, die mit abc beginnt
      abc$        jede Zeile, die mit abc endet
      \*          trifft jeden Stern
      [Mr]aus     trifft Maus und raus
      [[abc]      trifft [ (muß am Anfang stehen), a, b, c
      [KT]?ELLER 	trifft ELLER, TELLER, KELLER
      [^a-zA-Z]   schließt alle Buchstaben aus
      [0-9]$      trifft jede Zeile, die mit einer Zahl endet
      [0-9][0-9] 	trifft jede zweistellige Nummer
      H(e|a)llo   trifft Hallo und Hello
      (ab)?       trifft entweder ``ab'' oder nichts (``ab'' ist optional)
      ^$          trifft alle Leerzeilen
      \{n,m\}     trifft ein Muster mindestens n-mal und höchstens m-mal
      \n          referenziert obige Muster
      \<abc\>     trifft das eingeschlossene Muster nur, wenn es sich um ein separates Wort handelt
      \(abc\)     Die Klammern fassen Ausdrücke zusammen. Jede Zeile wird nach angegebenen. Muster durchsucht und jeder Treffer wird in einem Puffer gespeichert (max. 9 dieser Muster sind in einem Befehl möglich).

## grep 

* standard command

      $ grep -i 'linux' input.txt               : Case-Insensitive Search
      $ grep -i willy list                      : willy oder WILLY egal!
      $ grep -w 'is' input.txt                  : Whole-Word Search

      $ grep -v '[0-9]' input.txt               : Inverting the Search
      $ grep -o '/[^/*]*' input.txt             : Print Only the Matched Parts, starting /
      $ grep 'Wort[1-9]*\>' Datei               : find Wort, Wort1,Wort1234 etc
      $ grep '^Dec 10'  /var/log/messages       : find "Dec 10" in each line
      $ grep '\/var\/log\/' Datei               : find "/var/log" in the data

* grep as search file type

      $ grep -R "modBaseSetDict" *          : -R, -r, --recursive
      $ grep -Hrn "text" .                  : Search some text from all files inside a directory with line number
      $ grep -ri "hello" --include=*.cc     : contain "hello" in a file name but with cc    
      $ grep -ri "hello" --include=*.{cc,h} : cc or h

* grep every '<regular expression>' can be used in vi or grep

      $ grep -v [#] list                    : without # in a line
      $ grep '[^#]' list                    : without # in a line
      $ grep '^[^#]' list                   : without # first position in a line
      $ grep '^[^#]' list | grep -v ^$      : but will empty line will be disappeared!

      $ grep 'Wort[1-9]*\>' Datei           : find Wort, Wort1,Wort1234 etc
      $ grep 'Wort[1-9]\?\>' Datei          : find Wort1,Wort2
      $ grep '^Dec 10'  /var/log/messages   : find "Dec 10" in each line
      $ grep '1\{3,5\}' Datei               : find 1111 oder 11111
      $ grep '[0-9]\{3\}' Datei             : find any numbers 258 389
      $ grep '[A-Z]\{3\}' Datei             : find any string  ACB UIC
      $ grep '\$[0-9]\{3,\}\>' Datei        : find more than 3 digits $123 or $12345
      $ grep '\/var\/log\/' Datei           : find "/var/log" in the data
      $ grep 'c:\\windows\\' Datei          : find "c:\windows\"                "
      $ grep '[A-Z]\+' Datei                : find line contain any Capital character

      $ grep '^Linux' file                  : Displays all lines that start with Linux
      $ grep '$x' file                      : Displays all lines that end with an x
      $ grep '^null$' file                  : containing the word null by itself
      $ grep '[Ll]inux' file                : containing Linux, turbolinux, LinuxOS
      $ grep '[0-9][0-9][0-9]' file         : files that contain 3 consecutive digits
      $ grep '^[^0-9]' file                 : lines that do no begin with a numeral
      $ grep '\<[Ll]inux\>' file            : Linux, linux but not turbolinux, LinuxOS
      $ grep '.....' file                   : Matches a line with 5 or more characters
      $ grep '.' file                       : Displays all non blank lines
      $ grep '\.' file                      : Displays all lines that have a period
      $ grep -c '^$' file                   : Displays the number of empty lines

* grep advanced

      $ grep -F '*/opt*' input.txt          : Fixed string search
      $ grep '\*/opt\*' input.txt           : same but without -F
      $ grep -Fc '*' input.txt              : Count the Matching Lines
      $ grep -Rl 'boot' /var/log            : Recursively search a Directory  -l files-with-matches
      $ grep -A3 'report' input.txt         : Print Additional Context Lines After Match
      $ grep -B3 'report' input.txt         : Print Additional Context Lines Before Match

* Exclude Multiple Patterns 

      $ grep -ivw 'the\|every' /tmp/file  
      $ grep -ivw -e 'the' -e 'every' /tmp/file
      $ grep -wiv -f pattern_file file        : in pattern_file 'the' or 'every' is defined. 

* Delete Lines in a Text File That Contain a Specific String

      $ grep -v "Donghee" myfile.txt > tmpfile && mv tmpfile myfile.txt
      $ awk '!/Donghee/' myfile.txt > tmpfile && mv tmpfile myfile.txt

* Quiz, what is this?

      $ grep ^[/]                :  start with '/'  

* think about this what should be different?

      $ ls | grep Name.ext                 : dot matches any single character
      $ ls | grep Name\.ext                : therefore you need this to find Name.ext file 

* think

      Befehl    cat file  grep b.*g file  grep b.*g. file  grep ggg* file
      Resultat  big       big             bigger           bigger
                bad bug   bad bug         boogy
                bag       bag
                bigger    bigger
                boogy     boogy

* explain 

      $ grep "Fred\(ericke\)\? Feuerstein" textfile

          A) Wir wollen in einem Textfile alle Zeilen, die den Namen Fred Feuerstein und
          Fredericke Feuerstein enthalten. Das bedeutet der Teil ``ericke'' ist optional.
          Die Klammern bilden eine Gruppe. Das Fragezeichen bedeutet ein oder kein Vorkommen des vorherigen Musters.

      $ grep "([^()]*)"

          A) Hier werden Klammern innerhalb anderer Klammern ausgeschlossen:
          Trifft (hello) und (aksjdhaksj d ka) aber nicht x=(y+2(x+1))

      $ grep "[0-9]\{3\}[ -]\?[0-9]\{7\}" file
      
          A) Jetzt wollen wir nach sich wiederholenden Mustern suchen.
          Eine gutes Beispiel sind Telefonnummern. Wir suchen nach einer Vorwahl
          (3 Ziffern) und der Nummer (7 Ziffern), getrennt durch einen - , einem Leerzeichen oder garnicht.
          [0-9] steht für alle Zahlen, \{3\} besagt, daß sich das vorherige Muster 3 mal wiederholen soll.
          [ -]\? repräsentiert die Auswahl des Trennzeichens (Leerzeichen, - oder garnichts)

      $ grep "^[[:space:]]*Hallo[[:space:]]*$" file
      
          A) Angenommen, wir suchen eine Zeile in der nur das Wort ``Hallo`` steht. Es ist zudem noch möglich, daß sich vor und/oder hinter ``Hallo` Leerzeichen befinden. Eine Möglichkeit wäre folgendes ^ steht für den Zeilenanfang, $ für das Zeilenende.

      $ grep "Ich habe \(Schröder\|Stoiber\) gewählt" file
      
          A) Manchmal ist es nötig, Zeilen zu suchen, in denen entweder das Eine oder das Andere steht.
          \| entspricht einem logischen ODER.

      $ echo bla blub bla | grep '\(bla\).*\1'

          A) at man einmal ein Muster in \(...\) definiert, kann man es mit \Zahl erneut einsetzen.

* find Non-ASCII characters in text files
      
      $ grep --color='auto' -P -n "[\x80-\xFF]" sample.txt
      $ grep --color='auto' -P -n "[^\x00-\x7F]" sample.txt
      $ grep --color='auto' -n "[^[:ascii:]]" sample.txt

      $ pcregrep --color='auto' -n "[\x80-\xFF]" sample.txt
      $ pcregrep --color='auto' -n "[^[:ascii:]]" sample.txt

## sed
* standard

      $ sed -i 's/rumba/samba/g' data     : in-place, open->substitute->save
      $ sed '/^$/d' file                  : delete blank lines
      $ sed '3,5d' file                   : delete lines 3 throug 5
      $ sed -i '/donghee/d' myfile.txt    : deleting the matching lines, contains donghee

* option 

      -e script
      -i in-line
      -n to suppress default behaviour use this option

* options 

      Operator 	                                        Effekt
      [Muster/Adressraum]/p 	                            gibt den mit [Muster/Adressraum] gekennzeichneten Bereichs aus.
      [Adressraum]/d                                      Löschen des mit [angegebener Adressraum] gekennzeichneten Bereichs.
      s/Muster1/Muster2/                    substitute    Ersetze das erste in einer Zeile auftretende Muster1 durch Muster2.
      [Adressraum]/s/Muster1/Muster2/       substitute    Ersetze über einen angegebenen Adressraum das erste in einer Zeile auftrettende Muster1 durch
      [Adressraum]/y/Muster1/Muster2/       transform     Transformiere über einen angegebenen Adressraum, jedes Zeichen in Muster1 durch das korrespondierende Zeichen in Muster2 (äquivalent zum Befehl tr.)
      g                                     global        Wendet das vorherstehende Kommando auf jedes vorkommende Ersetzungsmuster einer Zeile an.
      [Anzahl]q 	                          quit          beendet sed nach ``Anzahl'' Zeilen.
       [Muster/Adressraum]/w file           write         Schreibt gefundene Zeilen in file.
      i$\backslash$ Text                    insert        fügt Text vorher ein
      a$\backslash$ Text                    append        fügt Text danach ein
      c$\backslash$ Text                    change        ersetzt durch Text
      =                                                   Gibt die aktuelle Eingabezeilennummer aus.
      {...}                                               Die von den Klammern eingeschlossenen und durch Zeilenende oder Semikolon getrennten Funktionen, werden als Einheit behandelt.

* Muster

      8d                      löscht die achte Zeile
      /^$/d                   löscht alle leeren Zeilen
      1,/^$/d                 löscht alles von Zeile 1 bis einschließlich der ersten leeren Zeile
      /GUI/d                  löscht alle Zeilen in denen ``GUI'' vorkommt
      /Jones/p                gibt nur Zeilen aus in denen der Name ``Jones'' vorkommt (mit -n)
      1,10 p                  gibt Zeilen 1 bis 10 aus (mit -n)
      /^begin/,/^end/p        gibt jede Zeile aus, die sich zwischen Zeilen die mit ``begin'' und ``end'' am Zeilenanfang befindet
      s/Windows/Linux/        ersetzt das erste in einer Zeile vorkommende, ``Windows'' mit ``Linux''
      s/BSOD/stability/g      setzt ``stability'' für jedes ``BSOD'' ein
      s/00*/0/g               ersetzt ``00'', ``000'', ...mit ``0''
      s/GUI//g                löscht ``GUI'' in jeder Zeile
      /^[0-9]/s/^/ /          alle Zeilen, die mit einer Zahl beginnen, um 3 Leerzeichen einrücken
      /^$/s/^/XXX/            alle leeren Zeilen mit ``XXX'' auffüllen
      10q                     zeigt die ersten 10 Zeilen an
      /^X/w file              schreibt alle Zeilen, die mit ``X'' beginnen, in file

* Der Stream-Editor arbeitet nach dem Texfilter-Prizip, die zu bearbeitende Datei kann als Befelszeilen-Argument angegeben werden, die Ausgabe erfolgt an der Standardausgabe
  
      $ sed 's/rumba/samba/g' data1 > data2         : rumba to samba then save
      $ sed -i 's/rumba/samba/g' data               : in-place, open->substitute->save
      $ set 'y/,/;/'   commadata > semicommadata    : commadata to semicommadata
      $ sed -e '/^$/d' -e '/^#/d' /etc/insserv.conf : del. containing number and empty line
      $ sed '25,47d' data1 > data2                  : del. line 25 upto 47
      $ sed 's/ue/uee/' data                        : substitue one time e to ee
      $ sed 's/ue/uee/g' data                       : whole sentence check
      $ sed -f sed-script umltext > htmltext        : -f allow to use script

      $ sed '2d;s/a/A/g' data            : Delete line 2 and substitue a for A
      $ sed '2,3s/a/A/g' data            : Substitute a for A on lines 2 to 3
      $ sed '2{ s/r/R/g; s/e/E/g }' data : do two substitutions for line 2
      $ sed '=' data | sed 'N;s/\n//g'   : print number then number put the line
      $ sed '3,5d' file                  : delete lines 3 throug 5
      $ sed '^# ' file                   : delete lines that start wit #
      $ sed 'y/abc/xyz/' file            : translate characters; a->x, b->y and c->z
      $ sed 's/^$/@/' file               : writes @ to all empty lines
      $ sed 's/"//g' file                : removes all double quotation
      $ sed '/^$/d' file                 : delete blank lines
      $ sed '/#keepme/!d' file           : delete lines that doesn't have "#keepme"
      $ sed '/^[ tab]*$/d' file          : delete lines that contain white space or tab
      $ sed '/^[[:blank:]]*$/d' file     : delete lines that contain white space or tab
      $ sed '/^[\.#]/d' file             : delete lines beginning with . or #
      $ sed 's/ */ /g' file              : substitute multiple spaces for a single space
      $ sed 's/ \{2,\}/ /g' file         : substitute multiple spaces for a single space
      $ sed '1,4s/abc/def/g' file        : substitutes abc for def on lines 1 to 4
      $ sed '11,20y/abc/@@@/' file       : translate a,b,or c on lines 11 through 20 for @
      $ sed '/123/{s/ab/AB/g}' file      : replaces ab for AB only on lines that have 123
      $ sed '/@#%/{ s/.*//g }' file      : remove nonblank line(.*) when lines contain @#%

* Add a head in data.csv file

      $ sed -i '1i ID,CREATED_AT,TIMESTAMP....' data.csv

* using sed, find non-ascii character in file 

      $ sed -i 's/[^\x0-\xB1]//g' sample.txt
      $ sed -n 'l' sample.txt                            : l-list  

* multiple commend

      $ sed -n 'p' input.txt                             : p-print
      $ sed -n -e '/line/ p' -e '/line/ q' input.txt     : multiple command, p-print, q-quit command
      $ sed -n '/line/ p; /line/ q' input.txt            : same 

* n and p application, and hold, get, and exahnge

      $ sed '' books_authors.txt
          Milk and Honey
          - Rupi Kaur
          Ariel
          - Sylvia Plath
      $ sed -n 'p;n' books_authors.txt
          Milk and Honey
          Ariel
      $ sed -n 'n;p' books_authors.txt
          - Rupi Kaur
          - Sylvia Plath
      $ sed -n 'n;s/^- //;p' books_authors.txt
          Rupi Kaur
          Sylvia Plath
      $ sed -n 'h;n;/Rupi Kaur/{g;p;}' books_authors.txt
          Rupi Kaur
      $ sed -E -n 'N; s/(.*)\n- (.*)/"\1" by \2/; p' books_authors.txt
          "Milk and Honey" by Rupi Kaur
          "Ariel" by Sylvia Plath
      $ sed -E -n 'h;n; /Kaur/ {G;s/- (.*)\n(.*)/"\2" by \1/;p;}' books_authors.txt
          "Milk and Honey" by Rupi Kaur

* Another example I 

      $ sed 's/    \([a-z]*)*/    \1/'               : 
      $ sed 's/\([a-z]*) \([a-z]*\)/\2 \1/'          : 
      $ sed 's/\([a-z]*) \1/\1/'                     :

      $ sed -i -e 's/wf95/wf96/g' wf96*
      $ sed -n '45,50p' filename                      # print line nos. 45-50 of a file

      $ sed 's/UNIX (TM)/Linux/g' file
      $ sed -e s/UNIX\ \(TM\)/Linux/g file
      $ sed -n -f muster_file inputfile >outputfile

      $ sed '/10,$/ s/WWJD/TWJD/g' file
      $ sed '/Josef/ s/WWJD/TWJD/g' file
      $ sed '1!s/WWJD/TWJD/g' file
  
* Another example II

      $ ls -1 bbb* | sed -e 's/bbb\(.*\)/mv "&" eee\1/' | sh
      $ ls -1 *root | sed -e 's/\(.*\)root/mv \1root \1root.00/' | sh
      $ ls -1 *root | sed -e 's/\(.*\)root/mv \1root ..\//'

      $ ls -1 *root.01 | sed -e 's/\(.*\)root.01/.\/hist_add \1root \1root.00 \1root.01/'  //ok
      $ ls -1 *root.00 | sed -e 's/\(.*\)root.01/.\/hist_add \1root \1root.00 \1root.01/'  //not work

* Another example III

      Kniffeliger wird die Angelegenheit, wenn es sich um die Optionen i, a und c handelt:

      #!/usr/bin/sed -nf
      /ganz bestimmter Text/{
            i\
            Text gehört davor
            a\
            Text der danach stehen soll
      }

* Another example IV

      Kommando und Ergebnis sehen dann so aus:

      $ echo 'ganz bestimmter Text' | sed -f scriptdatei
      | Text gehört davor
      | ganz bestimmter Text
      | Text der danach stehen soll

      geht auch ohne scriptdatei.

      $ echo 'ganz bestimmter Text' | \
        sed -e '/ganz bestimmter Text/{;i\' \
        -e 'Text gehört davor' -e 'a\' -e 'Text der danach stehen soll' -e ' }'

* Another example V

      #!/bin/sh
      for file in `ls wf95*`
      do
          echo $file | sed -e 's/wf95\(.*\)/mv "&" wf96\1/' | sh
      done

* print data blocks by sed: [based on this site]( https://www.baeldung.com/linux/print-lines-between-two-patterns)

      $ sed -n '/DATA BEGIN/, /DATA END/p' input.txt                                     : all between begind and end
      $ sed -n '/DATA BEGIN/, /DATA END/{ /DATA BEGIN/! { /DATA END/! p } }' input.txt   : exclude first and last line. 

      with awk command, one can build an equivalent command 
      $ awk '/DATA BEGIN/{ f = 1; next } /DATA END/{ f = 0 } f' input.txt

      To avoid some corner case as only printing the complete data blocks, this function can be used.
      $ awk 'f { if (/DATA END/){
                    printf "%s", buf; f = 0; buf=""
                } else
                    buf = buf $0 ORS
             }
           /DATA BEGIN/ { f = 1 }' input2.txt
      
      Same extention is necessary for sed case with below
      $ sed -n '/DATA BEGIN/,/DATA END/{/DATA END/{s/.*//;x;s/^\n//;p;d};/DATA BEGIN/!H }' input2.txt

* Let's think about this!

      $ cat bistru.txt
        line 1
        line 2
        line 3
        line 4
        line 5
        line 6
        line 7
        line 8
        line 9

      $ sed -n 'p;N;N' bistru.txt
        line 1
        line 4
        line 7

## tr
* translate or delete character

      $ cat file | tr -s '[:blank:]' : exchange spaces as one space
      $ cat file | tr -s e           : exchange ee/eee as e
      $ cat file | tr n x            : exchange n as x
      $ cat file | tr -d n           : delete n character in file
      $ cat file | tr a-z A-Z        : exchange small to big
      $ cat file | tr -d [:blank:]   : delete space and tap, normal case use
      $ cat file | tr -d [:space:]   : delete all white space, only one line after this
      $ cat file | tr -d \a          : pipeton aus

      $ tr -d '\r' < vmargs.txt | tr '\n' ' '    : Join lines and separate with spaces

* find non-ascii character in file

      $ tr -d '[:print:]' < sample.txt

## awk
* Print 3 and 4th field by awk in a file

      $ awk '{print $3 "\t" $4}' marks.txt

* this is differ

      $ awk 'NR%3 != 0' filename
      $ awk 'NR % 3 == 1' filename

* this is same!

      $ awk 'NR%2 !=0' zzz.txt
      $ awk 'NR%2 ==1' zzz.txt

* Delete Lines in a Text File That Contain a Specific String

      $ awk '!/Donghee/' myfile.txt > tmpfile && mv tmpfile myfile.txt
      $ gawk -i inplace '!/Donghee/' myfile.txt

<br/><a name="Scripting"></a>
==============================================================================================

# Bash scripting
1. Bash variable and parameter
2. Bash command and parameter
3. Echo
4. Streams, pipes, and redirection
5. Bash fucntion

## Bash variable and parameter

* Spezielle Typen von Variablen

      \$
      \\
      \"

* explanation

      ${var} = foo/bar/bay         :
      ${var%/*} = foo/bar          :
      ${var%%/*} = foo             :
      ${var#*/} = bay              : delete all the variables
      ${var##*/} = bar/bay         : delete all the variables

      "${Option%%=*}"
      "${Option##*=}"

* Spezialparameter

      $*   bezeichnet alle Positionsparameter von 1 an. In Anfuehrungszeichen gesetzt, steht $* fuer ein einziges Wort, bestehend aus dem Inhalt aller Positionsparameter, mit dem ersten internen Feldseperator (meistens Leerzeichen, Tab und Zeilenende) als Trennzeichen.
      $@   bezeichnet alle Positionsparameter von 1 an. In Anfuehrungszeichen gesetzt, wird es durch die Werte der einzelnen Positionsparameter (jeweils ein einzelnes Wort) ersetzt.
      $#   Anzahl der Positionsparameter
      $?   Rueckgabewert (Status) des zuletzt ausgefuehrten Kommandos
      $-   steht fuer die Optionsflags (von set oder aus der Kommandozeile).
      $$   Prozessnummer der Shell
      $!   Prozessnummer des zuletzt im Hintergrund aufgerufenen Kommandos
      $0   Name des Shellscripts
      $_   letztes Argument des zuletzt ausgefuehrten Kommandos

* Arithmetik

      z=`expr $z + 3` # Aufruf des externen Kommandos expr
      let z=z+3       # Aufruf des internen Kommandos
      let "z += 3"    # Mit Quotes sind Leerzeichen und special operators erlaubt.
      z=$(($z+3))     # neue verkuerzte Schreibweise (ab Version 2.0)
      z=$[$z+3]       # alte Schreibweise

      + -         Vorzeichen
      ! ~         logische und bitweise Negation
      * / %       Multiplikation, Division, Modulo
      + -         Addition und Subtraktion
      << >>       bitweise links und rechts-Shift-Operation
      <= >= <>    Vergleiche
      == !=       gleich und ungleich

      &           bitweise Addition
      ~           bitweise XOR
      |           bitweise ODER
      &&          bitweise UND
      ||         logisches ODER

* Condition

      =~                                                   : bit true, then will do
      if ! [[ "${argInstanceName}" =~ ^(BW|IW).$ ]];       : BW. or IW.
      if [[ $str =~ ^/ ]];                                 : start from "/"

* Logic

      $ false && echo howdy!

      $ true && echo howdy!
        howdy!
      $ true || echo howdy!
  
      $ false || echo howdy!
        howdy!

* Options

      -r file     #Check if file is readable.
      -w file     #Check if file is writable.
      -x file     #Check if we have execute access to file.
      -f file     #Check if file is an ordinary file (not a directory, a device file, etc.)
      -s file     #Check if file has size greater than 0.
      -d file     #Check if file is a directory.
      -e file     #Check if file exists.  Is true even if file is a directory.
      -z file     #True  if string is null (or empty). this succeeds if file is unset

## Bash command and parameter
* kommand wie macht man

      option1 option2 : parameter1,2 des Befehls, parameter0 $0 ist commando
      $ <commando> $1 $2            
      $ ba TAB TAB...               : dann vorschlagte Kommandos mit "ba*" angezeigt wird.

* command

      $(Kommando) oder `Kommando`

      $(ls -l /tmp)
      newvariable=$(printf "foo")

      Bedingte Ausfuehrung
      COMMAND1 && COMMAND2
        stellen eine logische UND-Verknuepfung dar. Wurde Kommando1 fehlerfrei ausgefuehrt
        (exit status 0 heißt Abarbeitung ohne Fehler), wird auch Kommando2 ausgefuehrt.
      COMMAND1 || COMMAND2
        Stellen eine logische ODER-Verknuepfung dar. Kommando2 wird nur ausgefuehrt,
        wenn bei Kommando1 ein Fehler aufgetreten ist.

      if grep someuser /etc/passwd; then
          # do something
      fi
      if ( w | grep someuser | grep sqlplus ); then
       # someuser is logged in and running sqlplus
      fi
      if $(grep ERROR /var/log/messages); then
          # send alerts
      fi

* Parametererweiterung

      ${Parameter}
      ${Parameter:-default}
      ${Parameter:=default}
      ${Parameter:?err_msg}
      ${Parameter:+alt_value}
      ${Parameter:Offset:Laenge}
      ${#Parameter}
      ${var#Pattern} und ${var##Pattern}
      ${var%Pattern} und ${var%%Pattern}
      ${var/Pattern/Replacement} und ${var/Pattern//Replacement}

* Array

      #!/bin/bash
      array=( zero one two three four five )
      array[6]="Dieser Text ist ein Element des Arrays"

      echo ${array[0]}                # zero
      echo ${array[1]}                # one
      echo ${array:0}                 # zero
                                      # Parametererweiterung, erstes Element.
      echo ${array:1}                 # ero
                                      # Parametererweiterung, erstes Element,
                                      # Start an Position #1 (2. Buchstabe).
      echo ${array[1]:1}              # ne
                                      # Parametererweiterung, zweites Element,
                                      # Start an Position #1 (2. Buchstabe).
      echo ${#array[2]}               # 3
                                      # Laenge des dritten Elements.
      element_count=${#array[@]}      # oder
      element_count=${#array[*]}      # Anzahl der Elemente: 7

## Echo

* standard

      $ echo $HISTSIZE
      $ echo $HISTFILESIZE
      $ echo $PS1 : primaeres Prompt
      $ echo $PS2 : sekundaeres Prompt

* Ruckgabewert des letzten Kommandos

      $ echo $?

* Shell expands double quotes ", however single quotes ' are not expanded  "

      $ echo "$SHELL" '$SHELL'   ->   /bin/bash  $SHELL
      $ echo '$USER'             ->   $USER
      $ echo "$USER"             ->   kang


* Echo usage 1

      example="Hello"

      $example       Hello
      "$example"     Hello
      \$example      $example
      '$example'     $example

      echo "The first character of PATH is ${$PATH:0:1}"    <- wrong
      echo "The first character of PATH is ${PATH:0:1}"     <- correct

      Variable_1=10
      Variable_2="Der Mond ist ein gruener Kaese."
      Variable_3="A B  C    D"
      Variable_4=$(hostname)
      echo \$Variable_1 = $Variable_1
      echo "\$Variable_2 = $Variable_2"
      echo '$Variable_1 + $Variable_2' = ${Variable_1}${Variable_2}
      echo $Variable_3
      echo "$Variable_3"
      echo $Variable_4

      ---------------------------------------------------------------
      $Variable_1 = 10
      $Variable_2 = Der Mond ist ein gruener Kaese.
      $Variable_1 + $Variable_2 = 10Der Mond ist ein gruener Kaese.
      A B C D
      A B  C   D
      asterix
      ---------------------------------------------------------------

* Echo usgae 2

      leer=
      default="voll"
      string="1234567890"
      array=( zero one two three four five )

      echo ${leer-$default}   # gibt nichts aus, denn $leer ist definiert
      echo ${undef-$default}  # gibt "voll" aus, denn $undef ist nicht definiert
      echo ${leer:-$default}  # gibt "voll" aus (:)

      default_filename=generic.data
      : ${1:?"Dateiname wird auf generic.data gesetzt."}       # Fehlermeldung, wenn $1 fehlt

      filename=${1:=$default_filename} # setzen des Parameters
      leer=${leer:+$default}           # sollte leer nicht NULL sein, wird er mit "voll" !" belegt

      echo ${string:0:1}               # von links beginnend mit 0 und einem Zeichen: 1
      echo ${string:(-3):2}            # von rechts und 2 Zeichen: 89

      laeng_string=${#string}          # ergibt 10
      echo ${#array}                   #  Laenge des ersten Elements: 4
      element_count=${#array[@]}       # oder
      element_count=${#array[*]}       # Anzahl der Elemente: 6

      var1=abcd12345abc6789
      pattern1=a*c                     # wildcard trifft alles zwischen 'a' und 'c'
      pattern2=b*9                     # alles zwischen 'b' und '9'
      echo ${var1#$pattern1}           # d12345abc6789
      echo ${var1##$pattern1}          # 6789
      echo ${var1%$pattern2}           # abcd12345a
      echo ${var1%%$pattern2}          # a
      echo ${pattern1/abc/ABC}         # "abcd12345abc6789" -> "ABCd12345abc6789"
      echo ${pattern1//abc/ABC}        # "abcd12345abc6789" -> "ABCd12345ABC6789"

* Echo usage 3

      $ echo $PPID : Parent process ID of the current process
      $ echo $$    :        process ID of the current process

      When passing to a child shell, double quotes are expended before passing command (in the parent shell), while single quotes are expended in the child process
      $ bash -c echo 'parent $$ $PPID'            >
      $ bash -c "echo parent $$ $PPID"            > parent 23033 23011
      $ bash -c 'echo child $$ $PPID'             > child 25798 23033
      $ echo "$USER_/this is my user name"        > /this is my user name
      $ echo "${USER}_/this is my user name"      > root_/this is my user name

      Runs a command that replaces the current shell
      $ echo $$        -------> 27316
      $ bash
      $ echo $$        -------> 27369
      $ exec ls
        anaconda-ks.cfg 
        clearlooks.tar.gz 
        Desktop 
        install.log 
        set1 
        um2
        bluecurve.tar.gz 
        declare1 
        icons 
        install.log.syslog 
        um1 
        umdois
      $ echo $$       --------> 27316

## Streams, pipes, and redirection
* print out to the monitor

      /dev/null 2>&1

      1>/dev/null
      2>/dev/null
      &>/dev/null

* Example) Find the root user

      grep ^root: /etc/passwd >/dev/null 2>&1
      if [ $? -neq 0 ]; then
          echo "root was not found - check the pub at the corner"
      fi

      oder more simple form

      if ! grep ^root: /etc/passwd >/dev/null 2>&1; then
          echo "root was not found - check the pub at the corner"
      fi

* Use streams, pipes and redirects

      standard-eingabe und -ausgabe STDIN(0) -> STDOUT(1) -> STDERR(2)
      Es ist moeglich, die Ein-u.ausgabekanaele umzuleiten.
      Ausgabeumleitung geht mit > (Datei)
      Eingabeumleitung geht mit < (Datei)

      Bei Ausgabeumleitung wird eine bereits bestehande Datei des gleichen Namens ueberschreiben
      $ ls -lR /etc > etcfiles
      $ ls -lR /etc/ 2> etcfiles.errs
      $ ls -lR /etc 2>> etcfiles

      configure ">>" meaning whether cannot overwrite or can overwrite by set!
      $ set -o noclobber       : not allow
      $ set +o noclobber       :     allow

      Doppelte Ausgabeumleiungszeichen haengen die Ausgabe am eine bestehende Datei an
      $ cat << string
      $ >a
      $ >b
      $ >string

      Redirection, can use "&" to send both stdout and stderr to a file ("&>" or "&>>")
      $ ps >  file                : create or overwrite
      $ ps >> file                : create or append
      $ ps &> file                : stdout and stderr into file
      $ cmd > file 2>&1           : stdout and stderr into file
      $ cmd 1> file1 2> file2     : stdout into 1 and stderr into 2
      $ ls /etc >/dev/null 2>&1   : no print

      Pipes with tee kann man Datenstrom in einer Textdatei ausgeben. Both display to monitor and also to the file, important!
      $ grep "kernel" /var/log/messages | tee kernel_messages.txt

## Bash fucntion
### example of bash functions
* function I

      #!/bin/bash
      multiply ()                       # multipliziert die uebergebenen Parameter
      {                                 # Anzahl der Parameter ist variabel
          local product=1
          until [ -z "$1" ]             # Until nutzt den ersten uebergebenen Parameter !"
          do
              let "product *= $1"
              shift
          done
          echo $product                 # wird nicht auf STDOUT ausgegeben,
      }                                 # wenn es an eine Variable uebergeben wird
      mult1=15383; mult2=25211
      val1=`multiply $mult1 $mult2`
      echo "$mult1 X $mult2 = $val1"    # 387820813

* function II 

      #! /bin/bash
      myadd() {
          # $1 erstes Argument
          tmp=0
          args=$@
          for i in $args do
            tmp=`expr $tmp + $i`
          done
          return $tmp
      }
      # main
      myadd 1 2 3 $VAR
      RES=$?
      myadd $RES 5 6 $VAR2
      RES=$?

* function III

      $ function addiere {let summe=$1+$2; echo -e "Summe ist $summe"}

### function: display modified date of file
      
      add this into the ~/.bashrc
      function findnamed {
          find ${2:-.} -name "*$1*" -printf "%M %-6u %-6g  %-${3:-40}p %TY-%Tm-%Td %TH:%TM\n"
      }
     
      $ findnamed '*json' project-root/ 35

### funciton: renaming

* renamer 1

      ---------------------------------------------------
      #!/bin/sh
      hist_add coral$1.root coral-1{0001,1001,1002,1003,1004,2001,2002,2003,7002,7003,7004,8001,8002,8003,8004}-$1.root

* renamer 2

      ---------------------------------------------------
      #!/bin/sh
      for i in 1 2 3 4;
          do mv mDST-2000${i}.root mDST-Lambda-2000${i}.root;
          do echo mDST-2200${i}.root mDST-${i}.root;
      done

* renamer 3

      ---------------------------------------------------
      #!/bin/bash
      criteria=$1
      re_match=$2
      replace=$3
      for i in $( ls *$criteria* );
      do
          src=$i
          tgt=$(echo $i | sed -e "s/$re_match/$replace/")
          mv $src $tgt
      done

### while Loop
* while 1

      while read -r line ; do
          if [[ "$line" = '*Good signature*' ]]; then
              echo ".......................so find"
          fi
      done < "${locVerificate}"

* while 2

      #!/bin/sh
      var0=0
      LIMIT=10
      while [ "$var0" -lt "$LIMIT" ]
      do
          echo -n "$var0 "             # -n suppresses newline.
          var0=`expr $var0 + 1`        # var0=$(($var0+1)) also works.
      done

* while 3

      #!/bin/sh
      while read -r line ; do
          if [[ "$line" = '*Good signature*' ]]; then
              echo ".......................so find"
          fi
      done < "${locVerificate}"

* for ... do

      #! /bin/sh
      for planet in Mercury Venus Earth Mars Jupiter Saturn Uranus
      do
          echo $planet
      done

      # oder aber auch
      NUMBERS="9 7 3 8 37.53"
      for number in `echo $NUMBERS`
      do
          echo "$number "
      done

* while und until

      #!/bin/sh
      var0=0
      LIMIT=10
      while [ "$var0" -lt "$LIMIT" ]
      do
          echo -n "$var0 "           # -n suppresses newline.
          var0=`expr $var0 + 1`      # var0=$(($var0+1)) also works.
      done
  
* case

      #!/bin/sh
      arch=$1
      case $arch in
          i386 ) echo "80386-based machine";;
          i486 ) echo "80486-based machine";;
          i586 ) echo "Pentium-based machine";;
          i686 ) echo "Pentium2+-based machine";;
          *    ) echo "Other type of machine";;
      esac

### Chronometer

* Chronometer in hour format, Shorter and faster...

      $ stf=$(date +%s.%N); 
            for ((;;));do ctf=$( date +%s.%N );
            echo -en "\r$(date -u -d "0 $ctf sec - $stf sec" "+%H:%M:%S.%N")";
            done

* Chronometer in hour format, Just add a format to chronometer in bash

      $ stf=$(date +%s.%N);st=${stf/.*/};sn=%{stf/*./};for ((;;));
                  do ctf=$( date +%s.%N );ct=${ctf/.*/};cn=${ctf/*./};
                  dtf=$(echo "scale=3; $ctf-$stf" | bc); dt=${dtf/.*/}; dt=${dt:=0};
                  echo -en "\r$(date -u -d @$dt "+%H:%M:%S.${dtf/*./}")";
                  done

* Chronometer : A way for tracking times in bash

      $ stf=$(date +%s.%N);st=${stf/.*/};sn=%{stf/*./};
                  for ((;;));do ctf=$( date +%s.%N );
                  ct=${ctf/.*/};cn=${ctf/*./};
                  echo -en "\r$(echo "scale=3; $ctf-$stf" | bc)";
                  done

<br/><a name="Question"></a>
==============================================================================================

# Q&A

* Q. only directory!

      # ls -d */
      $ find . -maxdepth 1 -type d
      $ tree -d -L 1
      $ echo */
      $ ls -l | grep '^d'
* Q. How offen some word fequently?

      $ cat words.txt 
        the day is sunny the the
        the sunny is is

      $ cat words.txt | tr -s ' ' '\n' | sort | uniq -c | sort -r | awk '{ print $2, $1 }'
      $ tr -s ' ' '\n' < words.txt | sort | uniq -c |sort -nr| awk '{print $2, $1}'

* Q. How would you print just the 10th line of a file?

      $ awk 'NR == 10' file.txt
      $ sed -n 10p file.txt

* Q. Find tel number like 987-123-4567 or (123) 456-7890

      $ sed -n -r '/^([0-9]{3}-|\([0-9]{3}\) )[0-9]{3}-[0-9]{4}$/p' file.txt
      $ grep -P '^(\d{3}-|\(\d{3}\) )\d{3}-\d{4}$' file.txt

* Q. Kommandosubstitution
      #!/bin/bash
      filename=/tmp/sample_file

      # cat sample_file
      #
      # 1 a b c
      # 2 d e fg

      declare -a array1
      array1=($(cat "$filename" | tr '\n' ' '))

      # Loads contents
      # of $filename into array1.
      # list file to stdout.
      # change linefeeds in file to spaces.

      echo ${array1[@]}

      # List the array:
      # 1 a b c 2 d e fg
      #
      #  Each whitespace-separated "word" in the file has been assigned to an element of the array.

      element_count=${#array1[*]}
      echo $element_count  

      # 8

* Q. convert 2 rows to 3 rows in a file
      name age
      alice 21
      ryan 30
       ----
      name alice ryan
      age 21 30

      ncol=`head -n1 file.txt | wc -w`
      for i in `seq 1 $ncol`
      do
    	    echo `cut -d' ' -f$i file.txt`
      done

* Q. Can you make a backup for your home directory

      #!/bin/bash
      SRCD="/home/"
      TGTD="/var/backups/"
      OF=home-$(date +%Y%m%d).tgz
      tar -cZf $TGTD$OF $SRCD

* Q. Send an email from the terminal when job finishes

      $ wait_for_this.sh; echo "wait_for_this.sh finished running" | mail -s "Job Status Update" username@gmail.com

* Q. function to find the fastest DNS server
      $ curl -s http://public-dns.info/nameserver/br.csv
          | cut -d, -f1 | xargs -i timeout 1 ping -c1 -w 1 {} | grep time
          | sed -u "s/.* from \([^:]*\).*time=\([^ ]*\).*/\2\t\1/g" | sort -n | head -n1


* Q. Get/List firefox bookmarks by tag from json backup

      $ ftagmarks(){
                  jq -r --arg t "$1" '.children[] as $i
                  |if $i.root == "tagsFolder"
                  then ([$i.children[]
                  as $j|{title: ($j.title), urls: [$j.children[].uri]}])
                  else empty end|.[]
                  as $k|
                  if ($k.title|contains($t))
                  then $k.urls
                  else empty end|.[]?' "$2";
      }

* Q. Find the package that installed a command

      $ whatinstalled() { which "$@" | xargs -r readlink -f | xargs -r dpkg -S ;}

      $ whatinstalled () {
                  local cmdpath=$(realpath -eP $(which -a $1 | grep -E "^/" | tail -n 1) 2>/dev/null)
                                      && [ -x "$cmdpath" ] && dpkg -S $cmdpath 2>/dev/null
                  | grep -E ": $cmdpath\$" | cut -d ":" -f 1; }

* Q. How can you make a copy of image file, cd image copy?

      $ dd if=/dev/sr0 of=debian.iso bs=1M

* Q. Move a folder and merge it with another folder

      $ gcp -r -l source/ destination/

* Q. Delete all but the last 1000 lines of file
      $ ex -c '1,$-1000d' -c 'wq' file

* Q. List wireless clients connected to an access point
      $ iw dev ath0 station dump

* Q. copy one partition to another with progress
      $ pv -tpreb /dev/sdc2 | dd of=/dev/sdb2 bs=64K conv=noerror,sync

* Q. Convert a Python interactive session to a python script
      $ sed  's/^\([^>.]\)/#\1/;s/^>>> //;s/^\.\.\./  /'

* Q. Retrieve a download count for URLs in apache logs
      $ zgrep 'pattern' /var/logs/apache2/access.log* | awk '{print $7}' | sort -n | uniq -c | sort -rn

* Q. Slow down the screen output of a command
      $ ls -lart | lolcat -a

* Q. Basic sed usage with xargs to refactor a node.js depdendency
      $ cat matching_files.txt | xargs sed -i '' "s/require('global-module')/require('..\/some-folder\/relative-module')/"

* Q. Automatically update all the installed python packages
      $ for i in `pip list -o --format legacy|awk '{print $1}'`;
            do pip install --upgrade $i; 
            done
