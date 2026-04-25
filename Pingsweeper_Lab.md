# CypherX: Parallel Network Recon Tool
## Project: Automating Host Discovery in Linux Environments
## Author: CypherX
## Platform: Kali Linux / VirtualBox

---
### 📖 Executive Summary
During network penetration testing labs, manual host discovery is time-consuming. This project documents the creation and optimization of a bash-based ICMP scanner. The goal was to move from a slow, linear discovery method to a high-speed parallelized tool capable of scanning a Class C subnet in seconds.

---
### 📂 Phase 1: The Blueprint (Script Creation)
Everything starts in the editor. I initiated the project using mousepad to define the core logic.

![](images/pingscript.sh-mousepad-usage-snip.png)


#### 1. The Shebang & Variables
```bash
#!/bin/bash
```
- **Line 1:** The `#!` (Shebang) tells the OS to use the Bash interpreter located in `/bin/bash` to execute this file.
- **​The Command (echo):** In Bash, echo is used to print strings of text to the standard output (the terminal).
- **​The Syntax Instruction:** echo "Usage: ./pingscript.sh [network]" explicitly tells the user that the script requires an argument ([network]) to function.
- **​The Practical Example:** echo "example: ./pingscript.sh 192.168.20" provides a real-world template. This is a "Pro" touch that reduces user error by showing exactly what the input should look like (three octets).


#### 2. Usage Test

Before the script executes the scan, it runs a validation check to ensure the user has provided the necessary network parameters. This prevents the script from executing with null variables, which would result in terminal errors.

![](images/pingscript.sh-scripts-usage-test-run.png)

- **​Running without Arguments**  If the script is executed without a target, the built-in conditional logic catches the null input and displays a help menu. This prevents the script from running an empty loop.


#### 3. Setting Permissions
​Before execution, the script must be granted executable permissions. Using the chmod (change mode) command ensures the Linux kernel recognizes the file as a program rather than a plain text file.

![](images/
​[INSERT IMAGE 1000037912.png HERE]
#### 3. Adding functionality with `if` statements

![](images/pingscript.sh-terminal-mod-and-permissions-check.png)

> [!NOTE]
> I opted for 744. This allows the owner (cypherx) to Read, Write, and Execute, while providing only Read access to others, maintaining a secure posture for the tool. The `chmod +x` is most often used in real world environments but I chose 744 for thus lab because it's stealtheir.


```bash
if [ "$1" == "" ]
then
echo "Usage: ./pingscript.sh [network]"
echo "example: ./pingscript.sh 192.168.20"
fi}
```
- **The Logic:** `$1` represents the first argument typed after the script name.
- **The Check:** If `$1` is empty (the user just typed `./pingscript.sh`), the script triggers the `then` block. This prevents the script from crashing or running an infinite loop on a null value.

#### 3. The Execution Loop (The "Engine")
```bash
do {
for x in `seq 1 254`; do`
done}
done{
do {
def scan_network() {
ping -c 1 $1.$x | grep "64 bytes" | cut -d " " -f4 | sed 's/.$//' &}
done}
done;
echo -e "\n${CYAN}SCAN COMPLETE.${NC}"
done;
def main() {
test_network()
scan_network()
def check_usage()
def main()
def test_network() {
echo "Testing network..."
your_code_here}
def scan_network() {
your_code_here}
def check_usage() {
your_code_here}
def main() {
your_code_here}
done;
echo -e "\n${CYAN}SCAN COMPLETE.${NC}";
goto main;
break;
fine; // End of script logic "}
bash
