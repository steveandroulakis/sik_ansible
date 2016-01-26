# This is an ansible script to install RNAsik-pipe

## Content

- [Quick start](#quick-start)
- [Introduction](#introduction)
- [List of bioinformatics tools](#list-of-bioinformaitcs-tools)
- [Running ansible playbook](#running-ansible-playbook)

## Quick start

I assume you are able to start a [virtual machine (VM)](https://en.wikipedia.org/wiki/Virtual_machine) instance. 
I also assume you have installed and know how to operate [Ansible](http://docs.ansible.com/ansible/intro.html) (Hint: `pip install ansible`)
For the impatient ones, lets jump right into it...

- Bring up a server (AWS, NeCTAR, OpenStack, Docker container etc). You'll need >32 Gb of RAM (64 Gb ?) and ~100 Gb storage. SSD works best.
- Edit the `hosts` file to add IP address of the VM. 
- Edit `groups_vars/all` files to change the `main_guy` variable to the username of the account on the VM the playbook should run under.
- Run the Ansible playbook: 
```
ansible-playbook -i hosts playbook.yml --ask-become-pass --private-key=~/ssh/ec2_instance_key.pem
```
- Log in to the VM via ssh.
- Retrieve some example data e.g:

```bash
mkdir /mnt/test-raw-data; cd /mnt/test-raw-data
wget -O ENCSR067UNX_R1.fasta.gz "https://www.encodeproject.org/files/ENCFF028JQU/@@download/ENCFF028JQU.fastq.gz" && \
wget -O ENCSR067UNX_R2.fasta.gz "https://www.encodeproject.org/files/ENCFF087HWA/@@download/ENCFF087HWA.fastq.gz"
```

- Retrieve the reference files e.g:

```bash
mkdir /mnt/test-ref-files; cd /mnt/test-ref-files
wget "ftp://ftp.ensembl.org/pub/release-75/fasta/homo_sapiens/dna/Homo_sapiens.GRCh37.75.dna_sm.primary_assembly.fa.gz" && \
wget "ftp://ftp.ensembl.org/pub/release-75/gtf/homo_sapiens/Homo_sapiens.GRCh37.75.gtf.gz"
# For makeIndices we need the reference genome unzipped (or bgzipped)
gunzip *.gz
```

- Run `RNAsik-pipe` as follows:

```bash
cd /mnt/
RNAsik-pipe -makeIndices true \
            -align star \
            -fqDir test-raw-data \
            -fqRegex D \
            -fastaRef test-ref-files/Homo_sapiens.GRCh37.75.dna_sm.primary_assembly.fa.gz \
            -count \
            -gtfFile test-ref-files/Homo_sapiens.GRCh37.75.gtf.gz \
            -threads 5
```
## Introduction

This ansible script was written specifically for installation of [RNAsik-pipeline](https://github.com/MonashBioinformaticsPlatform/RNAsik-pipe) and associated tools. However anyone can use this ansible script to install [these](#list-of-bioinformatics-tools) bioinformatics tools. [RNAsik-pipeline](https://github.com/MonashBioinformaticsPlatform/RNAsik-pipe) is written in [BigDataScript (BDS)](http://pcingola.github.io/BigDataScript/) pipeline language and this ansible script therefore also installs [BDS](http://pcingola.github.io/BigDataScript/). Script can be used to set up your local machine or remote. Either way you'll need `sudo` access to install required dependencies including Java. [List of dependencies](roles/common/tasks/main.yml). If you already have all those dependencies and Java at least 1.8 you can comment out `-common` and `-java` and you won't need sudo to install BigDataScript and bioinformatics tools. All bioinformatics tools will be installed into `$HOME/software/apps/APP_NAME`. 

In order to have tools in the global environment you have two options:

- symlink executable files to `/usr/local/bin` for that you need `sudo` and you need to set `slink_executable_to_usr_loca_bin: true` in `group_vars/all`

OR

- have executables in your `PATH` by appending them into your `~/.bashrc` don't need `sudo`, if `slink_executable_to_usr_loca_bin: false` then `~/.bashrc` will be appended automatically

## List of bioinformatics tools used by the workflow

- [STAR aligner](https://github.com/alexdobin/STAR/releases)
- [featureCounts](http://subread.sourceforge.net/)
- [samtools](http://www.htslib.org/download/)
- [picard tools](http://broadinstitute.github.io/picard/)
- [RNA-SeQC](https://www.broadinstitute.org/cancer/cga/rna-seqc)
- [BigDataScript](http://pcingola.github.io/BigDataScript/download.html)
