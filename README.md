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

- edit `host` file to add vm ip address, edit `groups_vars/all` files to change vm username
- [run ansible](#running-ansible-playbook)
- log into vm and run your tools as you like

## Introduction

This ansible script was written specifically for installation of [RNAsik-pipe](https://github.com/MonashBioinformaticsPlatform/RNAsik-pipe) and associated tools. However anyone can use this ansible script to install [these bioinformatic tools](#list-of-bioinformatics-tools). [RNAsik-pipe](https://github.com/MonashBioinformaticsPlatform/RNAsik-pipe) is written in [BigDataScript (BDS)](http://pcingola.github.io/BigDataScript/) pipeline language, which is also installed using this ansible script. This ansible script can be on your local or remote machines.

You will need `sudo` privilege to install `common.yml` dependencies. If you don't have such privilege you'll need to obtain [all of these](roles/common/tasks/main.yml) dependencies plus Java 8. You then can use `bio.yml` playbook to install all of the tools locally. This does not requires `sudo` privilege.

Once you've installed everything you'll need to get `RNAsik-pipe` into your environment.

- `export PATH=$HOME/software/source/RNAsik-pipe-1.3-beta/src/:$PATH` i.e make `RNAsik-pipe` available in your path

Optionally you can append `export` line into your `~/.bashrc` to make if permanently available in your environment.

`RNAsik-pipe` also has `sik.config` file that one can use to specify path to all required executables. This is why you don't have export any other tools into your path just RNAsik-pipe. This ansible script comes with the right `sik.config` file and no additional changes need to be made to that file.

#### Test run

- pull your raw data e.g `mkidr test-raw-data; cd test-data` and then:
    - `wget -O ENCSR067UNX_R1.fastq.gz "https://www.encodeproject.org/files/ENCFF028JQU/@@download/ENCFF028JQU.fastq.gz" 2> /dev/null &`
    - `wget -O ENCSR067UNX_R2.fastq.gz "https://www.encodeproject.org/files/ENCFF087HWA/@@download/ENCFF087HWA.fastq.gz" 2> /dev/null &`

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
            -prePro \
            -RNAseQC \
            -fastqc \
            -threads 5
```

## List of bioinformatics tools

- [BigDataScript](http://pcingola.github.io/BigDataScript/download.html)
- [STAR aligner](https://github.com/alexdobin/STAR/releases)
- [featureCounts](http://subread.sourceforge.net/)
- [samtools](http://www.htslib.org/download/)
- [Picard tools](http://broadinstitute.github.io/picard/)
- [gffutils](https://pypi.python.org/pypi/gffutils) python package, can use `(sudo) pip install gffutils`
- [RNA-SeQC](https://www.broadinstitute.org/cancer/cga/rna-seqc)

## Running ansible playbook

- to get everything installed at once with `sudo` privilege

```BASH
ansible-playbook -i hosts all.yml
```

- to install only dependencies with `sudo` privilege

```BASH
ansible-playbook -i hosts common.yml
```

- to install only BigDataScript, Tools and `RNAsik-pipe`

```BASH
ansible-playbook -i hosts bio.yml
```

Please note above command assume you have edited group_vars/all file with the correct user name (main_guy variable) AND you have ssh key set up for that remote machine and that user. If you don't have ssh keys you can use `--ask-pass` to specify users password on the command line
