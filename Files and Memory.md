# Files and Memory

## Volatility

```sh
# Commands volatility
volatility -f <image_dump> imageinfo
# specify profile
# e.g. –profile=WinXPSP2x86
volatility -f <image_dump> --profile=<your_profile> printkey -K "YOUR/REGISTRY/KEY/HERE"
# show connections
volatility -f <image_dump> --profile=<your_profile> connections
# search for PID match with connections
volatility -f <image_dump> --profile=<your_profile> pslist
# search for malware
volatility -f <image_dump> --profile=<your_profile> malfind
# dump the process
volatility -f <image_dump> --profile=<your_profile> procdump -p <PID> --dump-dir
volatility -f <image_dump> --profile=<your_profile> memdump -p <PID> --dump-dir
```

or 

```sh
python vol.py [plugin] -f [image] --profile=[profile] 
```

A little note on volatility. The old volatility runs on python 2. I've always experienced issues with volatility.
So, the best solution I found was using a docker image created by [@oste](https://hub.docker.com/r/oste/volatility2).

To run the image:
```sh
docker run --rm -v /your/path/to/your/data:/data oste/volatility2:amd64 volatility -f /data/your_memory_dump.vmem <PLUGIN>
```

There are also images of volatility3 available in [@sk4la/volatility3-docker](https://github.com/sk4la/volatility3-docker). They work very well, and also don't forget to also see the version in repository with community plugins.

```sh
# Commands volatility3
# dump process (exe and dll)
volatility3 -f <image_dump> -o <output_path> windows.dumpfiles ‑‑pid <PID>
# dump memory (most important one)
volatility3 -f <image_dump> -o <output_path>  windows.memmap ‑‑dump ‑‑pid <PID>
# command line
volatility3 -f <image_dump> windows.cmdline
# print registry key (example)
volatility3 -f <image_dump> windows.registry.printkey ‑‑key “Software\Microsoft\Windows\CurrentVersion”
# yara scan
volatility3 -f <image_dump> yarascan -y <path_to_yara_rule>
```

## dmde

It is a useful tool to recover data from various types of storage media, such as hard drives, flash drives, memory cards ...
Download [here](https://dmde.com/).

## tune2fs

Adjust tunable filesystem parameters on ext2/ext3/ext4 filesystems

```sh
# display parameters
tune2fs -l <file_system>
# intervals between filesystem checks
tune2fs -i 1w <file_system>
# convert ext2 to ext3
tune2fs -j <file_system>
```

## stat

Show info about a file or filesystem (blocks, Inodes).

```sh
stat <your_file_system>
stat -f <your_file_system>
```

## chainsaw

Chainsaw is available in the [following repository](https://github.com/WithSecureLabs/chainsaw).
Useful for examining event logs, with a plethora of options to run.

It is important to note that I have tried the tool with `.evt` files and it did not work. I don't know if the current version supports it though!

These are some irreplaceable commands for chainsaw that I've repeatedly used with log analysis:
```sh
# Search all the files in folder for the specific string `meow`
./chainsaw search meow -i logs/
# Searching for specific event 
# See list of events https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/
./chainsaw search -t 'Event.System.EventID: =4104' logs/
# Sigma rules are cool!
./chainsaw hunt logs/ -s sigma/ --mapping mappings/sigma-event-logs-all.yml --from "<TIMESTAMP>" --to "<TIMESTAMP>" --json
```

## Commands for ext3, ext4 filesystems

```sh
# recover deleted files from an ext3 or ext4 partition
extundelete disk-image --restore-all
# ext3
ext3grep --dump-name <device_name>
ext3grep --restore-all <device_name>
# ext4
ext4magic <device_name> -H -a $(date -d "-3hours" +%s)
ext4magic <device_name> -a <timepoint> -f / -j <file> -r -d <recovered_file>
```

## Modified Dockerfile for volatility 

This Dockerfile was used to analyze a specific Windows profile that has not been integrated in the default branch of volatility.

```Dockerfile
FROM ubuntu:latest

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y git curl nano vim python2.7 wget pcregrep libpcre++-dev python2-dev build-essential libdistorm3-dev yara libraw1394-11 libcapstone-dev capstone-tool tzdata && \
    curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py && \
    python2 get-pip.py && \
    pip2 install pycrypto distorm3 && \
    echo 'alias volatility="python2 /opt/volatility/vol.py"' >> /root/.shrc && \
    git clone https://github.com/volatilityfoundation/volatility.git /opt/volatility

WORKDIR /opt/volatility

RUN git checkout win10_19041

ENTRYPOINT ["python2", "vol.py"]
```
