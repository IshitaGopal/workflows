# Terminal 

## Get help/options associated with a function

```
man cp

q  # to close
```

## Filesystems

To list hidden files listed, add ```-a``` flag: 

```
ls -a
```

To view more information/'long form' about our files add ```-l```. This shows: 
1. Permissions 
2. User 
3. Group owner 
4. Date 

```
ls -l

# combines 
ls -la
```

## Relative Paths 
```.``` = current directory

```..``` = up 1 directory

```../..```= up 2 directory

```cd``` or ```cd ~``` = go to the home dir

## Create, copy, rename and delete files and dir: 

### Create:
```
# create 
touch test_file.txt

# open 
open test_file.txt

```

### Copy 

```
cp test_file.txt copy_file.txt

cp -R TestDir/ CopyDir/
```


### Rename/ Move 
```
# Rename
mv test_file.txt orig_file.txt

# Move file 
# Dont rename the file - just move it to a different dir
mkdir SubDir1
mv orig_file.txt SubDir1/ 

# Rename it as well
mv orig_file.txt SubDir1/test_file.txt 

```

### Delete 
- When using rm be careful. It does'nt go to the trashcan. When its gone, its gone.

```
rm copy_file.txt

rm -R CopyDir

# To force delete add f
rm -rf TestDir/ 
```

## Find things 


```
# find all the directories and no files in the current dir
find . -type d 
```

```
# find all the files and no directories in the current dir
find . -type f
```


```
# Search for an exact filename
find . -type f "test_1.txt"
find . -type f "*.py"
```

```
# Find files which start with test 
find . -type f -name "test*"
```

```
# Case insensitive 
find . -type f -iname "test*"
```


### Filtering on metadata

```
# All files modified in the last 10 minutes 
# -10 = less than 10 minutes 
# +10 = more than 10 minutes


find . -type f -mmin -10

# More than 10 min ago and less than 5 min ago 

find . -type f -mmin +1 -mmin -5 

# more than days ago +mtype20
# modified days 
find . -type f -mtime +20 
```

```
# Find by size over 5mb
find . -size +5M

find . -empty
```

### Filtering on permissions and changing them
```
# By permissions read/write/execute permissions
find . -perm 777 

# change user and group using execute chown (change owner); {} placeholder; + ends command  

find Wensite_Demo -exec coreyshaffer:www-data {} +  

# Run another commad to change permissions 
find Wensite_Demo -type d -exec chmod 775 {} +

find Wensite_Demo -type f chmod 666 {} +
``` 

### Filtering on files and jpg images and and deleting them
```
# Delete all img files that ends in .jpg and is at max 1 folder down

find . -type f -name "*.jpeg" - maxdepth 1 - exec rm {} + 
```

## Grep command

- Global regular expression print
- Allows us to search for text within files on our system 
- When there are no results , it just jumps to the next line 

Example, seach a if 'Jane Williams' is within a file

```
grep 'Jane Williams' names.txt
```

```
#  Only return whole words (dont return Jane Williamson)  
grep -w "Jane William" names.txt

# Make it cases insenstive
grep -wi "Jane William" names.txt

# Get line info 
grep -win "Jane William" names.txt
```

- See a certain number of lines before the match  

```
# Returns 4 lines before the match
grep -win -B 4 "Jane William" names.txt

# Returns 4 lines after the match
grep -win -A 4 "Jane William" names.txt

# Returns 2 lines before and after the match = context 
grep -win -C 2 "Jane William" names.txt

```

- Searching in all files in the current directory

```
grep -win -A 4 "Jane William" ./*

# Only in the text files
grep -win -A 4 "Jane William" ./*.txt 

# search in every single file and subdirectories 
grep -winr -A 4 "Jane William" ./
```

- Only return the files with matches, not the matches themselves
```
grep - wirl -A 4 "Jane William" .
```

- Number of matches in each file

```
grep -wirc "Jane William" 
```

### Pipe (pass) results to grep 
- search our history for git commits

```
history | grep "git commit"

history | grep "git commit" | grep "dotfile"
```

## Use regular expressions 


```
grep "...-...-...." names.text
```

## cURL
Allows us to:
- query urls from the command line 
- post form data
- authenticate users 
- save responses to files 
- test connection speeds
- test rest APIs; will return some json 
- can use different http methods when requesting a url

```
# Include header info (-i = include)
curl -i http://localhost:5000/methods

# Post info -fill a form (-d = data)
curl -d "firs=Corey& last=Shaffer" http://localhost:5000/methods

# Update data -- put method 
curl -X PUT "firs=Corey& last=Shaffer" http://localhost:5000/methods

# authentication - provide username and password 
curl -u corey:pass http://localhost:5000/secret
```

- Download the response 
```
# Download response (-o output)
curl -o test.jpeg http://localhost:5000/download
open test.jpeg

curl -o commits.json some_website

```

## rsync 
- Allows you to transfer and sync files efficiently across directories and machines 
- More efficient than copying files
- Only syncs files that are different

```
rsync Original/* Backup/

# Sync the contents of the entire dir 
rsync -r Original/ Backup/

# Sync the actual dir - Orignal dir 
rsync -r Original Backup/

# archime maintains permissions
rsync -a Original Backup/ 
```

- Check before you sync 
- dry run option + verbose option

```
rsync -av --dry-run Original Backup/
```

- Mirror the source directory and delete anything extra that is in the Backup/ that is not in the Orignal/

```
rsync -av --delete --dry-run Original Backup/
```

### sync files from a local and remote machine

- transfering data over the network 
- you have options to compress and see progress

```
rsync -zaP ~/source_dir @coreyms@192.168.56.100:~/public/

# check everything got there 
ssh coreyms@192.168.56.100
cd public/
ls
```

### sync files from a remote to a local machine
```
rsync -zaP @coreyms@192.168.56.100:~/source destination/

```

## Cron jobs 
- way to schedule commands to run on regular intervals 
- can be paired with rsync 



