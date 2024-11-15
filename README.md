# learn-linux-bash-cli
### Bash Command Line Tools: `grep`, `sed`, `awk`, and `jq` Demo

This guide demonstrates how to use the command line tools `grep`, `sed`, `awk`, and `jq` with different types of files on Linux. The following tools are often used for searching, transforming, and analyzing text data in files.

### Prerequisites
- You should have a Linux system (Ubuntu is used for this demo).
- Tools `grep`, `sed`, `awk`, and `jq` must be installed. If they're not installed, you can install them using the following commands:
```bash
sudo apt update
sudo apt install grep sed awk jq
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
```bash
{
  "name": "Jane",
  "age": 30
}
```
