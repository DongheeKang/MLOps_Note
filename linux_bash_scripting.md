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

### find I

      Necessariness tools and tips for security
      $ find / -perm -u+s           : SUID bit
      $ find / -perm +4000 -type f  : list of files with SUID bit
      $ find / -perm -g+s           : GUID bit
      $ find /usr -uid 0            : owned by root

### find II
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


<br/><a name="Scripting"></a>

# Bash scripting