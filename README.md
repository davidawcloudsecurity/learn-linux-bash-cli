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
### File Setup
We will create the following sample files for each tool:
```bash
logfile.txt - A simple log file.
data.csv - A CSV file.
data.json - A JSON file.
```
### 1. Create a Sample Log File: logfile.txt
```bash
echo -e "INFO User logged in\nERROR Invalid password\nINFO User logged out\nWARNING Disk space low" > logfile.txt
```
Use grep to search for all INFO log entries.
```bash
grep "INFO" logfile.txt
```
Use grep -l to search for all the logs that contains INFO entries.
```bash
grep -l "INFO" .
```
Output
```bash
INFO User logged in
INFO User logged out
```
### 2. Create a Sample CSV File: data.csv
The CSV file contains sample user data.
```bash
echo -e "id,name,age\n1,John,25\n2,Jane,30\n3,Joe,22" > data.csv
```
Use sed to change all occurrences of John to Johnny in the CSV file.
```bash
sed 's/John/Johnny/' data.csv
```
Output
```bash
id,name,age
1,Johnny,25
2,Jane,30
3,Joe,22
```
### 3. Create a Sample Text File: textfile.txt
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
### 4. Locate the specific file by its name and partial path
```bash
sudo find / -path '*/services/asset/dataset-sftp-export/approvals/example.js'
# insensitive
sudo find / -ipath '*/services/asset/dataset-sftp-export/approvals/list.js'
# faster result
sudo find /home -path '*/services/asset/dataset-sftp-export/approvals/list.js'
# Redirect Errors: Suppress permission-denied errors during the search
sudo find / -path '*/services/asset/dataset-sftp-export/approvals/list.js' 2>/dev/null
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
Output
```json
{
  "name": "Jane",
  "age": 30
}
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
### Using -E (Extended Regular Expressions)
OR
```bash
grep -e "INFO" -e "ERROR" logfile.txt
grep -E "ERROR|WARNING" logfile.txt
```
AND
```bash
grep "INFO" logfile.txt | grep "User"

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
### 4. Using awk for Logical AND
```bash
awk '/INFO/ && /User/' logfile.txt
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
#### 3.1 Replace value in json
```bash
tf_variables=$(echo "${tf_variables}" | jq --arg instance_name "$instance_name" '.account_name = $instance_name')
```
If tf_variables contains:
```json
{
  "account_name": "old_value"
}
```
And instance_name="new_value", the result will be:
```json
{
  "account_name": "new_value"
}
```
#### 3.2 Add new line and value in json
```bash
tf_variables=$(echo "$tf_variables" | jq --arg newarn "$new_arn" '. + {iam_arn: $newarn}')
```
Input JSON (tf_variables):
```json
{
  "account_name": "example_account"
}
```
Shell Variable ($new_arn):

plaintext
```json
"arn:aws:iam::123456789012:user/example"
```
Result (tf_variables):
```json
{
  "account_name": "example_account",
  "iam_arn": "arn:aws:iam::123456789012:user/example"
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
How to while loop one liner bash
```
while [ condition ]; do command; done
```
https://linuxsimply.com/bash-scripting-tutorial/loop/while-loop/while-loop-one-line/
