## What is hashcat?
Hashcat is a powerful password recovery tool used for cracking hashes through CPU or GPU acceleration. It supports numerous hash types (e.g., NTLM, SHA‑512 crypt, bcrypt) and attack modes (dictionary, mask, hybrid). Hashcat is widely used in penetration testing and digital forensics to audit password strength.
## shows cracked hashes in UN:PW format  
```bash
# format 5 only shows passwords or remove –username to only show PW 
Hashcat –m <hash##> --username –show –outfile-format 2 the/hash/location
``` 
NOTE: all cracked creds are in hashcat.potfile

# Show all hash modes (grep for specific ones like 'LM' or '1800')
Important modes: 0 MD5, 1800 or 1400 in /etc/shadow, 3200 bcrypt, 7500 & 13100 kerberos, 2500/22000 wpa/wpa2
```bash
hashcat -hh | grep 'LM'
```
# Benchmark your system's cracking speed for a specific hash type
hashcat -w 3 --benchmark -m <hash#>

# Dictionary attack (mode 0)
```bash
hashcat -w 3 -a 0 -m <hash#> /path/to/uncracked.hashes /path/to/wordlist.txt
```
# Hybrid attack (dictionary + mask)
```bash
hashcat -w 3 -a 6 -m <hash#> /path/to/uncracked.hashes /path/to/wordlist.txt ?d?d -j 'c'
```
# Dictionary + rule-based attack
Efficient rules: dive.rule, rockyou-30000.rule, NSAKEY.v2.dive.rule
```bash
hashcat -w 3 -a 0 -m <hash#> /path/to/uncracked.hashes /path/to/wordlist.txt -r /rules/best66.rule
```
### Make your own rules and masks ###
syntaxing for masks
| Placeholder | Character Set | Example | Description |
| ``?l`` | Lowercase letters (``a–z``) | ``?l?l?l?l`` | 4‑letter lowercase |
| ``?u`` | Uppercase letters (``A–Z``) | ``?u?l?l?l`` | Capitalized word |
| ``?d`` | Digits (``0–9``) | ``?d?d?d?d`` | 4‑digit PIN |
| ``?s`` | Symbols (``!@#$%^&*``) | ``?l?l?s?d`` | Word + symbol + digit |
| ``?a`` | All printable ASCII | ``?a?a?a?a`` | Full brute‑force |
| ``?b`` | Binary (0x00–0xFF) | Rarely used | Raw byte brute‑force|

syntaxing for rule custom rules.
```bash
#CAP first letter and append 12 to every word
echo "c $1$2" > MyCustom.rule
```
| ``c`` | Capitalize first letter | ``password`` → ``Password`` | Uppercase first character |
| ``C`` | Capitalize entire word | ``password`` → ``PASSWORD`` | All uppercase |
| ``$X`` | Append character ``X`` | ``$1`` → ``password1`` | Adds suffix |
| ``^X`` | Prepend character ``X`` | ``^!`` → ``!password`` | Adds prefix |
| ``sXY`` | Substitute ``X`` with ``Y`` | ``sa@`` → ``p@ssword`` | Leetspeak replacement |
| ``l`` | Lowercase entire word | ``PASSWORD`` → ``password`` | All lowercase |
| ``r`` | Reverse word | ``password`` → ``drowssap`` | Useful for mirrored patterns |
| ``d`` | Duplicate word | ``pass`` → ``passpass`` | Doubles the candidate |
| ``t`` | Toggle case for each letter | ``PaSs`` → ``pAsS`` | Alternating case |
| ``:`` | No operation (used for readability) | ``:`` | Keeps word unchanged |
