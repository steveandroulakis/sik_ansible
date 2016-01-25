# This is an ansible script to install RNAsik-pipe

## Content

- [Quick start](#quick-start)
- [Introduction](#introduction)
- [List of bioinformatics tools](#list-of-bioinformaitcs-tools)
- [Running ansible playbook](#running-ansible-playbook)

## Quick start

I assume you know how to start new [virtual machine (vm)](https://en.wikipedia.org/wiki/Virtual_machine) instance. I also assume you know how to install and operate ansible for more refer to [ansible documentations](http://docs.ansible.com/ansible/intro.html). For the impatient once lets jump right into it...

- bring up a vm (AWS, NeCTAR, OpenStack, etc)

_depending on how you want to run this playbook the order of the next couple of steps will change, but I can see you are a smart cookie, so you'll be fine_

- edit `host` file to add vm ip address, edit `groups_vars/all` files to change vm user
- run ansible
- log into vm
- pull your raw data e.g `mkidr test-raw-data; cd test-data` and then:
    - `wget -O ENCSR067UNX_R1.fasta.gz "https://www.encodeproject.org/files/ENCFF028JQU/@@download/ENCFF028JQU.fastq.gz" 2> /dev/null &`
    - `wget -O ENCSR067UNX_R2.fasta.gz "https://www.encodeproject.org/files/ENCFF087HWA/@@download/ENCFF087HWA.fastq.gz" 2> /dev/null &`
- pull your reference files e.g `mkdir test-ref-files; cd test-ref-files` and then:
    - `wget ftp://ftp.ensembl.org/pub/release-75//fasta/homo_sapiens/dna/Homo_sapiens.GRCh37.75.dna_sm.primary_assembly.fa.gz`
    - `wget ftp://ftp.ensembl.org/pub/release-75//gtf/homo_sapiens/Homo_sapiens.GRCh37.75.gtf.gz`
- run `RNAsik-pipe` as follows:
```BASH
RNAsik-pipe -align star \
            -fqDir test-raw-data \
            -fqRegex D \
            -fastaRef test-ref-file/Homo_sapiens.GRCh37.75.dna_sm.primary_assembly.fa.gz \
            -count \
            -gtfFile test-ref-files/Homo_sapiens.GRCh37.75.gtf.gz \
            -threads 5
```

## Introduction

This ansible script was written specifically for installation of [RNAsiik-pipeline](https://github.com/MonashBioinformaticsPlatform/RNAsik-pipe) and associated tools. However anyone can use this ansible script to install [these](#list-of-bioinformatics-tools) bioinformatics tools. [RNAsiik-pipeline](https://github.com/MonashBioinformaticsPlatform/RNAsik-pipe) is written in [BigDataScript (BDS)](http://pcingola.github.io/BigDataScript/) pipeline language and this ansible script therefore also installs [BDS](http://pcingola.github.io/BigDataScript/). Script can be used to set up your local machine or remote. Either way you'll need `sudo` access to install required dependencies including Java. [List of dependencies](roles/common/tasks/main.yml). If you already have all those dependencies and Java at least 1.8 you can comment out `-common` and `-java` and you won't need sudo to install BigDataScript andbioinformatics tools. All bioinformatics tools will be installed into `$HOME/software/apps/APP_NAME`. 

In order to put have tools in the global environment you have two options:

1. symlink executable file to `/usr/local/bin` for that you need `sudo`

OR

2. have executables in your `PATH` by appending them into your `~/.bashrc` don't need `sudo`

## List of bioinformatics tools

- [STAR aligner](https://github.com/alexdobin/STAR/releases)
- [featureCounts](http://subread.sourceforge.net/)
- [samtools](http://www.htslib.org/download/)
- [picard tools](http://broadinstitute.github.io/picard/)
- [RNA-SeQC](https://www.broadinstitute.org/cancer/cga/rna-seqc)
- [BigDataScript](http://pcingola.github.io/BigDataScript/download.html)

## Running ansible playbook

```BASH
ansible-playbook -i hosts playbook.yml
```

