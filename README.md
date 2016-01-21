# This is an ansible script to install RNAsik-pipe

## Content

- [Introduction](#introduction)
- [List of bioinformatics tools](#list-of-bioinformaitcs-tools)
- [Running ansible playbook](#running-ansible-playbook)

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
