# Other Stuff

## VirusTotal

[Virus Total](https://www.virustotal.com/gui/home/upload)

Upload any file and check the **Details** Tab for **MD5**, **SHA-1**, **SHA-256** hashes.

## file

```bash
file <your_file_here>
# mime type
file -i <your_file_here>
```

## binwalk

```bash
# extract all files
binwalk --dd='.*' <your_file_here>
# signatures
binwalk --signature <your_file_here>
# carve
binwalk -e --carve <your_file_here>
```

## tsurugi

Download [.iso](https://tsurugi-linux.org/downloads.php)
Based on ubuntu with preinstalled [Tools](https://tsurugi-linux.org/documentation_tsurugi_linux_tools_listing_2020.php).

## php

When there is obfuscated .php code I always use [OnlinePHP.io](https://onlinephp.io/)

## obfuscated shell

**Windows PowerShell ISE** is always useful when there is an obfuscated shell command present. It is common place with the following pattern:

```bash
powerShell.exe -WindowStyle hiddeN -ExecuTionPolicy ByPasS -enc JAB7AFsAfgBAAH0AIAA9gibberish
```
