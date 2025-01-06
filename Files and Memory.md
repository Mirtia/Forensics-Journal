# Files and Memory

## Volatility

```bash
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

```bash
python vol.py [plugin] -f [image] --profile=[profile] 
```

A little note on volatility. The old volatility runs on python 2. I've always experienced issues with volatility.
So, the best solution I found was using a docker image created by [@oste](https://hub.docker.com/r/oste/volatility2).
I only managed to pull the image with ``im64`` tag. 
To run the image:
```bash
docker run --rm -v /your/path/to/your/data:/data oste/volatility2:amd64 volatility -f /data/your_memory_dump.vmem <PLUGIN>
```

## dmde

It is a useful tool to recover data from various types of storage media, such as hard drives, flash drives, memory cards ...
Download [here](https://dmde.com/).

## tune2fs

Adjust tunable filesystem parameters on ext2/ext3/ext4 filesystems

```bash
# display parameters
tune2fs -l <file_system>
# intervals between filesystem checks
tune2fs -i 1w <file_system>
# convert ext2 to ext3
tune2fs -j <file_system>
```

## stat

Show info about a file or filesystem (blocks, Inodes).

```bash
stat <your_file_system>
stat -f <your_file_system>
```

## chainsaw

Chainsaw is available in the [following repository](https://github.com/WithSecureLabs/chainsaw).
Useful for examining event logs, with a plethora of options to run.

## Commands for ext3, ext4 filesystems

```bash
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

```Dockerfile
FROM ubuntu:latest

RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y git curl nano vim python2.7 wget pcregrep libpcre++-dev python2-dev build-essential libdistorm3-dev yara libraw1394-11 libcapstone-dev capstone-tool tzdata && \
    curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py && \
    python2 get-pip.py && \
    pip2 install pycrypto distorm3 && \
    echo 'alias volatility="python2 /opt/volatility/vol.py"' >> /root/.bashrc && \
    git clone https://github.com/volatilityfoundation/volatility.git /opt/volatility

WORKDIR /opt/volatility

RUN git checkout win10_19041

ENTRYPOINT ["python2", "vol.py"]
```
