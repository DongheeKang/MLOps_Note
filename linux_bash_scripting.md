# Linux bash scripting
    * Bash shell fundamental 
    * Bash scripting

### Contents
  * [Bash shell fundamental](#Shell)  
  * [Bash scripting](#Scripting)

    Network and Security issues are covered in another session
    https://github.com/DongheeKang/MLOps_Note/blob/main/linux_network.md

<br/><a name="Shell"></a>

# Bash shell fundamental

1. find
2. Regular expression
3. sed
4. tr
5. awk

### find
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

      $ find . -type d -name "TMVA"
      $ find . -type f -empty
      $ find . -type f -exec grep "example" '{}' \; -print
      $ find . -type f -print | xargs grep "example"

* some options for displaying

      -print
      -print0      : find a file contained null character
      -path
      -empty
      -executable
      -atime       : access
      -ctime       : chagned
      -mtime       : modified
      -maxdepth    : directory maximum depth level

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

* advanced

      $ find . -size -10 -exec ls -l {} \;
      $ find . -size -10 -exec rm {} \;
      $ find set_11 -size -2k -iname '*Dg*' -exec ls -alh {} \;
      $ find set_10 -size -400c -exec ls -alh {} \;          : c is a block

      $ find . -type f -size -1000 -printf "%s:%h%f\n" | grep "par"
      $ find . -type f -size -1000c | grep "pid.root"
      $ find / -type f -size -1k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }'

      $ find . -name core -exec ls -l {} ＼;
      $ find . -name '*.root' -exec ls -l {} \;
      $ find . -name '*.mp3' | xargs rm
      $ find . -name "*.c"   | xargs grep foo1foo2foo3
      $ find / -name 'et*' -type d
      $ find . -name ＼*.c -atime +30 -exec ls -l {} ＼;
      $ find . -name "node_mod" -exec rm -rf '{}' \;      : recursively remove "node_mod" directories

      $ find -wholename "*/query/*.json"                  :  find matching whole name example

### find: security and ownership
* Necessariness tools and tips for security

      $ find / -perm -u+s           : SUID bit
      $ find / -perm +4000 -type f  : list of files with SUID bit
      $ find / -perm -g+s           : GUID bit
      $ find /usr -uid 0            : owned by root

* finding by owership

      $ find / -user kang                   : ownership by kang
      $ find / -perm +4000                  : Document with SUID-bit format
      $ find / -size +500M | xargs ls -l    : Datei grosser als 500M

### find: Display Modified Date

      $ find . -name '*json*' -printf "%-25p %t\n"
      $ find . -name "*json*" | xargs -d '\n' stat -c "%-25n %y"
      $ find . -name "*json*" | xargs -d '\n' ls -ld
      $ find . -name "*json*" -exec stat -c "%-25n %y" {} \;
      $ find . -name "*json*" -exec ls -ld {} \;

### Find files that have been modified recently

* via Find

      $ find . -mtime -1
      $ find /home/sports -mmin +120
      $ find /home/sports -type f -mmin +120
      $ find . -type f -mmin -120 -mmin +60        : 60 min < time < 120 min

      $ find . -type f -newermt 2019-07-24
      $ find . -type f -newermt 2019-07-24 ! -newermt 2019-07-25

      $ find . -type f -newermt "-24 hours" 
      $ find . -type f -newermt "-10 minutes" 
      $ find . -type f -newermt "1 day ago" 
      $ find . -type f -newermt "yesterday"

* via ls  
      $ ls -lt
      $ ls -lt | grep 'Jul 27'
      $ ls -lt | grep '17:'
      $ ls -ltR

### Delete files older than certain time

      $ find . -name "access*.log" -type f -mmin +15 -delete        : 15 mins
      $ find . -name "access*.log" -type f -mtime +5 -delete        : 5 days
      $ find . -name "access*log" -exec rm {} \;                    : for older find version
      $ find . -name "access*log" -exec rm -i {} \;                 : will delete with a prompt
      $ find . -delete -name file.txt                               : !!!!!!! will delete all in the directory!!!!!!

### Find Time related
* using options

      $ find /home -amin 10  : zugegriffenen Datei vor n Min
      $ find /home -cmin 20  : geaendertene Datei
      $ find /home -mtime 1  : vor 1 mal 24 Stunden gmodifiziert
      $ find /home -ctime 2  : vor weniger als 2 mal 24 Stunden geaendert

      $ find . -iname "*" -daystart -mtime 1 -type f
      $ find . -iname "*" -atime 10 -type -f
      $ find . -iname "*" -atime -60 -type -f

* explain

      To find 7 hours with .db
      $ find . -mtime 7 -iname ".db"

      To find 10 Sep 
      $ find ./ -type f -ls | grep '10 Sep'

      To find all files modified on the 7th of June, 2007:
      $ find . -type f -newermt 2007-06-07 ! -newermt 2007-06-08

      To find all files accessed on the 29th of september, 2008:
      $ find . -type f -newerat 2008-09-29 ! -newerat 2008-09-30

      To find files which had their permission changed on the same day:
      $ find . -type f -newerct 2008-09-29 ! -newerct 2008-09-30

### finding links to a file

  	$ -> lrwxrwxrwx 1 root root 5 Feb 7 15:36 saran-l -> saran
  	$ find . -lname saran
  	$ find . -samefile mke2fs
  	$ find . -inum 421422

### find change from htm to html within the folder

      $ find /var/www/ -name *.html -exec chmod 500 {} \;
      $ find . -name '*.htm' -exec mv '{}' '{}l' \;

### Difference Between 'sort | uniq' and 'sort -u'



### Regular expression

* Regular expression

      [:blank:]   : all horizontal whitespace
      [:xdigit:]  : all hexadecimal digits
      [:alpha:]   : all letters
      [:alnum:]   : Alphanumeric characters
      [:alnum:] 	alle alphanumerischen Zeichen [A-Za-z0-9]
      [:alpha:] 	alle Buchstaben [A-Za-z]
      [:blank:] 	ein oder mehrere Leerzeichen und Tab
      [:cntrl:] 	alle Kontrollzeichen wie z.B. <newline>
      [:digit:] 	alle dezimalen Zahlen [0-9]
      [:graph:] 	alle druckbaren Zeichen (ASCII 33 - 126) ohne das Leerzeichen
      [:print:] 	alle druckbaren Zeichen
      [:lower:] 	alle Kleinbuchstaben [a-z]
      [:upper:] 	alle Großbuchstaben [A-Z]
      [:space:] 	Leerzeichen und horizontales Tab
      [:xdigit:] 	alle hexadezimalen Zahlen [0-9A-Fa-f]
     
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
      [a-zA-Z]      : Match any letter
      [^0-9A-Za-z]  : Any non alphanumeric character
      [A-Z][a-z]*   : An uppercase character followed by zero or lower case character
      \$[ 0-9]*\.[0-9]\{2\} : Matches $xx.xx or $ xxx.xx,  whereas the chracters following $ could be a space or number
      [0-9]\{2}-[0-9]\{4\}-[0-9]\{3\} : Matches numbers on the pattern xx-xxxx-xxx

-  Zeichenklassen,  Wiederholungsoperatoren

      . 	      ist ein Platzhalter und bezeichnet jedes einzelne Zeichen außer das Zeilenende.
      [abc$] 	trifft alle aufgeführten Zeichen.
      a-c 	      bezeichnet alle Zeichen im angegebenen Limit.
      [^exp] 	trifft alle Zeichen außer den angegebenen.
      ^abc 	      trifft das angegebene Muster, wenn es am Zeilenanfang steht.
      abc$ 	      trifft das angegebene Muster, wenn es am Zeilenende steht.
      \ 	      Maskierung des folgenden Zeichens ($\rightarrow$2.3.2)
      .aus 	      trifft Haus, raus, Maus, Laus,...
      xy*z 	      trifft auf xy...was auch immer...z
      ^abc 	      jede Zeile, die mit abc beginnt
      abc$ 	      jede Zeile, die mit abc endet
      \* 	      trifft jeden Stern
      [Mr]aus 	trifft Maus und raus
      [[abc] 	trifft [ (muß am Anfang stehen), a, b, c
      [KT]?ELLER 	trifft ELLER, TELLER, KELLER
      [^a-zA-Z] 	schließt alle Buchstaben aus
      [0-9]$ 	trifft jede Zeile, die mit einer Zahl endet
      [0-9][0-9] 	trifft jede zweistellige Nummer
      H(e|a)llo 	trifft Hallo und Hello
      (ab)? 	trifft entweder ``ab'' oder nichts (``ab'' ist optional)
      ^$ 	      trifft alle Leerzeilen
      \{n,m\} 	trifft ein Muster mindestens n-mal und höchstens m-mal
      \n 	      referenziert obige Muster
      \<abc\> 	trifft das eingeschlossene Muster nur, wenn es sich um ein separates Wort handelt
      \(abc\) 	Die Klammern fassen Ausdrücke zusammen. Jede Zeile wird nach angegebenen. Muster durchsucht und jeder Treffer wird in einem Puffer gespeichert (max. 9 dieser Muster sind in einem Befehl möglich).


### grep 

* grep command

      $ grep -R "modFactoryOsCPUCoresCheck"  *    :
      $ grep -R "modBaseSetDict"  *               :
      $ grep 'Wort[1-9]*\>' Datei                 : find Wort, Wort1,Wort1234 etc
      $ grep '^Dec 10'  /var/log/messages         : find "Dec 10" in each line
      $ grep '\/var\/log\/' Datei                 : find "/var/log" in the data
      $ grep -Hrn "text" .                        : Search some text from all files inside a directory

* Quiz, what is this?

    $ grep ^[/]

* think

    ls | grep Name.ext
    ls | grep Name\.ext

* think

    Befehl 	  cat file 	grep b.*g file 	grep b.*g. file 	grep ggg* file
    Resultat  big 	big 	            bigger 	      bigger
              bad bug   bad bug 	      boogy
              bag 	bag
              bigger    bigger
              boogy     boogy

* grep every '<regular expression>' can be used in vi or grep

      $ grep -i w.lly  list                 : willy oder WILLY egal!
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
      $ grep -c '^$' file                   : Displays the number of empty lines
      $ grep '^null$' file                  : containing the word null by itself
      $ grep '[Ll]inux' file                : containing Linux, turbolinux, LinuxOS
      $ grep '[0-9][0-9][0-9]' file         : files that contain 3 consecutive digits
      $ grep '^[^0-9]' file                 : lines that do no begin with a numeral
      $ grep '\<[Ll]inux\>' file            : Linux, linux but not turbolinux, LinuxOS
      $ grep '.....' file                   : Matches a line with 5 or more characters
      $ grep '.' file                       : Displays all non blank lines
      $ grep '\.' file                      : Displays all lines that have a period


* Explain

      $ grep "Fred\(ericke\)\? Feuerstein" textfile
      Wir wollen in einem Textfile alle Zeilen, die den Namen Fred Feuerstein und
      Fredericke Feuerstein enthalten. Das bedeutet der Teil ``ericke'' ist optional.
      Die Klammern bilden eine Gruppe. Das Fragezeichen bedeutet ein oder kein Vorkommen des vorherigen Musters.

      $ grep "([^()]*)"
      Hier werden Klammern innerhalb anderer Klammern ausgeschlossen:
      Trifft (hello) und (aksjdhaksj d ka) aber nicht x=(y+2(x+1))

      $ grep "[0-9]\{3\}[ -]\?[0-9]\{7\}" file
      Jetzt wollen wir nach sich wiederholenden Mustern suchen.
      Eine gutes Beispiel sind Telefonnummern. Wir suchen nach einer Vorwahl
      (3 Ziffern) und der Nummer (7 Ziffern), getrennt durch einen - , einem Leerzeichen oder garnicht.
      [0-9] steht für alle Zahlen, \{3\} besagt, daß sich das vorherige Muster 3 mal wiederholen soll.
      [ -]\? repräsentiert die Auswahl des Trennzeichens (Leerzeichen, - oder garnichts)

      $ grep "^[[:space:]]*Hallo[[:space:]]*$" file
      Angenommen, wir suchen eine Zeile in der nur das Wort ``Hallo`` steht. Es ist zudem noch möglich,
      daß sich vor und/oder hinter ``Hallo` Leerzeichen befinden. Eine Möglichkeit wäre folgendes
      ^ steht für den Zeilenanfang, $ für das Zeilenende.

      $ grep "Ich habe \(Schröder\|Stoiber\) gewählt" file
      Manchmal ist es nötig, Zeilen zu suchen, in denen entweder das Eine oder das Andere steht.
      \| entspricht einem logischen ODER.

      $ echo bla blub bla | grep '\(bla\).*\1'
      Hat man einmal ein Muster in \(...\) definiert, kann man es mit \Zahl erneut einsetzen.





### sed

* option 

      -e script
      -i in-line

* sed

      $ sed -i 's/rumba/samba/g' data     : in-place, open->substitute->save
      $ sed '/^$/d' file             	   	: delete blank lines
      $ sed '3,5d' file         	        : delete lines 3 throug 5

* Add a head in data.csv file

      $ sed -i '1i ID,CREATED_AT,TIMESTAMP....' data.csv


* sed script example

      #!/bin/sh
      for file in `ls wf95*`
      do
          echo $file | sed -e 's/wf95\(.*\)/mv "&" wf96\1/' | sh
      done

* options 

      Operator 	                                        Effekt
      [Muster/Adressraum]/p 	                            gibt den mit [Muster/Adressraum] gekennzeichneten Bereichs aus.
      [Adressraum]/d 	                                  Löschen des mit [angegebener Adressraum] gekennzeichneten Bereichs.
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

- Muster

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


* Another example

      $ sed 's/    \([a-z]*)*/    \1/'
      $ sed 's/\([a-z]*) \([a-z]*\)/\2 \1/'
      $ sed 's/\([a-z]*) \1/\1/'
      $ sed -i -e 's/wf95/wf96/g' wf96*
      $ sed -n '45,50p' filename                  # print line nos. 45-50 of a file

      $ sed 's/UNIX (TM)/Linux/g' file
      $ sed -e s/UNIX\ \(TM\)/Linux/g file
      $ sed -n -f muster_file inputfile >outputfile

      $ sed '/10,$/ s/WWJD/TWJD/g' file
      $ sed '/Josef/ s/WWJD/TWJD/g' file
      $ sed '1!s/WWJD/TWJD/g' file
  
* Another examples

      $ ls -1 bbb* | sed -e 's/bbb\(.*\)/mv "&" eee\1/' | sh
      $ ls -1 *root | sed -e 's/\(.*\)root/mv \1root \1root.00/' | sh
      $ ls -1 *root | sed -e 's/\(.*\)root/mv \1root ..\//'

      $ ls -1 *root.01 | sed -e 's/\(.*\)root.01/.\/hist_add \1root \1root.00 \1root.01/'  //ok
      $ ls -1 *root.00 | sed -e 's/\(.*\)root.01/.\/hist_add \1root \1root.00 \1root.01/'  //not work

* examples

      Kniffeliger wird die Angelegenheit, wenn es sich um die Optionen i, a und c handelt:

      #!/usr/bin/sed -nf
      /ganz bestimmter Text/{
            i\
            Text gehört davor
            a\
            Text der danach stehen soll
      }

* examples

      Kommando und Ergebnis sehen dann so aus:

      $ echo 'ganz bestimmter Text' | sed -f scriptdatei
      | Text gehört davor
      | ganz bestimmter Text
      | Text der danach stehen soll

      geht auch ohne scriptdatei.

      $ echo 'ganz bestimmter Text' | \
        sed -e '/ganz bestimmter Text/{;i\' \
        -e 'Text gehört davor' -e 'a\' -e 'Text der danach stehen soll' -e ' }'

* think about this!
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


### tr

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

### awk

* this is differ

      awk 'NR%3 != 0' filename
      awk 'NR % 3 == 1' filename

* this is same!

      awk 'NR%2 !=0' zzz.txt
      awk 'NR%2 ==1' zzz.txt










<br/><a name="Scripting"></a>

# Bash scripting