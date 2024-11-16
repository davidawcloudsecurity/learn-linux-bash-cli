# learn-linux-bash-cli
### Bash Command Line Tools: `grep`, `sed`, `awk`, and `jq` Demo

This guide demonstrates how to use the command line tools `grep`, `sed`, `awk`, and `jq` with different types of files on Linux. The following tools are often used for searching, transforming, and analyzing text data in files.

### Prerequisites
- You should have a Linux system (Ubuntu is used for this demo).
- Tools `grep`, `sed`, `awk`, and `jq` must be installed. If they're not installed, you can install them using the following commands:
```bash
sudo apt update
sudo apt install grep sed awk jq coreutils
```
Use grep to search for all INFO log entries.
```bash
grep "INFO" logfile.txt
```
Use sed to change all occurrences of John to Johnny in the CSV file.
```bash
sed 's/John/Johnny/' data.csv
```
This is a simple text file that we'll use with awk for processing structured text.
```bash
echo -e "Name: John\nAge: 25\nCity: New York\nName: Jane\nAge: 30\nCity: London" > textfile.txt
```
Use awk to print the names of the individuals from the file.
```bash
awk -F ": " '{print $2}' textfile.txt
```
Output
```bash
John
25
New York
Jane
30
London
```
To extract only the names:
```bash
awk -F ": " 'NR % 3 == 1 {print $2}' textfile.txt
```
Output
```bash
John
Jane
```
### Create a Sample JSON File: data.json
The JSON file contains information about users and their preferences.
```bash
echo -e '{"users": [{"name": "John", "age": 25}, {"name": "Jane", "age": 30}]}'> data.json
```
Use jq to extract the names from the JSON file.
```bash
jq '.users[].name' data.json
```
Output
```bash
"John"
"Jane"
```
Use jq to filter users who are older than 25.
```bash
jq '.users[] | select(.age > 25)' data.json
```
## Advanced grep Examples
### 1. Search Recursively in Directories
Use grep to search for the term ERROR in all .log files within a directory.
```bash
grep -r "ERROR" *.log
```
### 2. Search with Context
Display 2 lines before and after each match using -C.
```bash
grep -C 2 "Disk space" logfile.txt
```
Output:
```sql
INFO User logged out
WARNING Disk space low
INFO User logged in
```
### 3. Use Regular Expressions
Search for lines that begin with INFO and end with in.
```bash
grep -E "^INFO.*in$" logfile.txt
```
## Advanced sed Examples
### 1. Perform Multiple Edits
Change all occurrences of ERROR to CRITICAL and WARNING to ALERT.
```bash
sed -e 's/ERROR/CRITICAL/g' -e 's/WARNING/ALERT/g' logfile.txt
```
### 2. Delete Lines Matching a Pattern
Remove all lines containing INFO.
```bash
sed '/INFO/d' logfile.txt
```
### 3. Insert Text After a Line
Add a message after lines containing CRITICAL.
```bash
sed '/CRITICAL/a\Please check immediately.' logfile.txt
```
## Advanced awk Examples
### 1. Conditional Filtering
Print rows from a CSV file where age is greater than 25.
```bash
awk -F, '$3 > 25 {print $0}' data.csv
```
### 2. Perform Calculations
Sum the ages in the CSV file.
```bash
awk -F, 'NR>1 {sum += $3} END {print "Total age:", sum}' data.csv
```
### 3. Modify Output Format
Format the CSV file into a table with column headers.
```bash
awk -F, 'BEGIN {printf "%-5s %-10s %-5s\n", "ID", "Name", "Age"} \
         NR>1 {printf "%-5s %-10s %-5s\n", $1, $2, $3}' data.csv
```
Output:
```bash
ID    Name       Age  
1     John       25   
2     Jane       30   
3     Joe        22
```
## Advanced jq Examples
### 1. Pretty-Print JSON
Format JSON output for better readability.
```bash
jq '.' data.json
```
### 2. Extract Specific Fields
Get a list of user names as a JSON array.
```bash
jq '[.users[].name]' data.json
```
Output:
```json
[
  "John",
  "Jane"
]
```
### 3. Modify JSON Data
Add a new field to each user.
```bash
jq '.users[] += {"status": "active"}' data.json
```
Output:
```json
{
  "name": "John",
  "age": 25,
  "status": "active"
}
```
### cut - Extract Specific Columns
Extract the "name" column from the CSV file.
```bash
cut -d, -f2 data.csv
```
Output:
```bash
name
John
Jane
Joe
```
### sort and uniq - Sorting and Deduplication
Sort the names and remove duplicates.
```bash
cut -d, -f2 data.csv | sort | uniq
```
### xargs - Build Commands from Input
Use xargs to list detailed information for a set of files.
```bash
echo "logfile.txt data.csv" | xargs ls -lh
```
### find - Search for Files
Find all .log files modified in the last 7 days.
```bash
find . -name "*.log" -mtime -7
```
### tee - Save and Display Output
Save grep results to a file while displaying them.
```bash
grep "ERROR" logfile.txt | tee errors.txt
```
### Combining Tools: Power of Pipelines
Find all users older than 25 from data.json, format them as a CSV, and save to a file.
```bash
jq -r '.users[] | select(.age > 25) | [.name, .age] | @csv' data.json | tee filtered_user
```
```bash
{
  "name": "Jane",
  "age": 30
}
```
