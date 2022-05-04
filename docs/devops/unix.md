# Unix

- [Useful Unix commands for data science](http://www.gregreda.com/2013/07/15/unix-commands-for-data-science/)
- [Basic Unix Shell Commands for the Data Scientist](http://practical-data-science.blogspot.com/2012/09/basic-unix-shell-commands-for-data.html)


## basic commands

```bash
hostname
pwd                     # current working directory
ls                      # list files
ls -l                   # list files with details
ls -a                   # list all files (including hidden)
ls -la                  # list all files (including hidden) with details

control + R                         # find history
chmod +x $file                      # make file executable
chmod 755 -R $folder                # change folder rights

mv $old_file_name $new_file_name                 # rename
cp $file_name $new_folder                        # copy
rm $file_name                                    # delete file
rm -R $folder_name                               # delete folder
find . -type f -name ".*.swp" -exec rm -f {} \;  # remove .swp hidden file

cat $file_name | wc -l                      # check rows of data in file
grep "jessie" result.csv > jessie_res.csv   # grep all lines with "jessie" into file

date
date -u
diff $file1 $file2
man $command
```

## make changes to alias

```bash
vi ~/.bash_profile
source ~/.bash_profile
```

## remote screen

```bash
screen     # create a remote screen
screen -ls # list all remote screens
screen -d  # detach
screen -r  # resume
screen -S $screen_name -X quit
```

## check file difference

```bash
path1=/Users/git/project_folder1
path2=/Users/git/project_folder2

file=script.py
file=coordinator.xml
file=workflow.xml
diff $path1/$file.py $path2/$file
```

## check file extension

```bash
if [[ $file == *.txt ]]
```

## vim

```bash
vi $filename
esc: wq!         # write and quit
esc: /$keyword   # find keyword in file
esc: 100dd       # delete 100 rows
:$               # go to the end of file
```
