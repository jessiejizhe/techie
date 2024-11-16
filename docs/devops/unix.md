- [Useful Unix commands for data science](http://www.gregreda.com/2013/07/15/unix-commands-for-data-science/)
- [Basic Unix Shell Commands for the Data Scientist](http://practical-data-science.blogspot.com/2012/09/basic-unix-shell-commands-for-data.html)


# terminal

```bash
hostname
man $command
pwd                     # current working directory
ls                      # list files
ls -l                   # list files with details
ls -a                   # list all files (including hidden)
ls -la                  # list all files (including hidden) with details
control + R             # find history
chmod +x $file          # make file executable
chmod 755 -R $folder    # change folder rights


# files
vi ~/.bash_profile
source ~/.bash_profile
sudo vi ~/.zshrc
source ~/.zshrc

alias sshdev='ssh devvm00000.facebook.com'
alias echo_scp='echo "scp devvm00000.facebook.com:/home/jessieji"'

# check dates
date			# local time
date -u			# utc time


# remote screen
screen     # create a remote screen
screen -ls # list all remote screens
screen -d  # detach
screen -r  # resume
screen -S $screen_name -X quit
```

# bash files

```bash
# edit files
mv $old_file_name $new_file_name                 # rename
cp $file_name $new_folder                        # copy
rm $file_name                                    # delete file
rm -R $folder_name                               # delete folder
find . -type f -name ".*.swp" -exec rm -f {} \;  # remove .swp hidden file


# remove prefix in file names
for file in prefix_*; do mv "$file" "${file#prefix_}"; done

# check file diff
diff $file1 $file2

path1=/Users/git/project_folder1
path2=/Users/git/project_folder2
file=script.py
file=coordinator.xml
file=workflow.xml
diff $path1/$file.py $path2/$file


# check file extension
if [[ $file == *.txt ]]


# grep
grep "jessie" result.csv > jessie_res.csv   # grep all lines with "jessie" into file


# cat
cat $file_name                              # print content to terminal
cat $file_name | less                       # open file neatly
cat $file_name | wc -l                      # check rows of data in file



# count unique values in the 2nd column in file.csv
cut -f2 file.csv | sort | uniq | wc -l

# display all unique values in the 2nd column in file.csv
awk '{ a[$2]++ } END { for (b in a) { print b } }' file.csv


# echo
echo "(copy paste)" | sed 's/ //g' | awk -F'|' '{print $2 ", " $3}'
echo "(copy paste)" | sed 's/ //g' | awk -F'|' '{print $2 "\t" $3}'
```


# vim

```bash
vi $filename

esc: wq!         # write and quit
esc: q!			 # quit w/o saving

esc: /$keyword   # find keyword in file
esc: 100dd       # delete 100 rows

:1				 # go to the beginning of file
:20				 # go to line 20
:$               # go to the end of file
```
