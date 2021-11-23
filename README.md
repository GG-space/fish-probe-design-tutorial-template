# FISH probe design: a tutorial

This tutorial shows how to setup `ifpd` on a local machine, and run a simple query for
probe design.

## Requirements

`ifpd` was developed to run on Unix machines with a shell. Thus, it works natively with
Linux and Mac, but requires a few extra steps on Windows.

### Windows: setup WSL

If your operating system is Windows, you need to instal Windows Subsystem for Linux (WSL,
more details [here](https://docs.microsoft.com/en-us/windows/wsl/about)) by following the
instructions at these links:

* [WSL install](https://docs.microsoft.com/en-us/windows/wsl/install)
* [Ubuntu on Microsoft Store](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6#)

### Install `pipx`

Then, install `pipx` (a Python package manager that installs packages in separate
environments while exposing CLI hooks). More details on the installation process are
available [here](https://github.com/pypa/pipx#install-pipx).

If you receive an error such as `/usr/bin/python3: No module named pip`, you need to
install `pip`. See [here](https://pip.pypa.io/en/stable/installation/) for more details.
On Ubuntu this can usually be achieved with something like `sudo apt install python-pip`.

You might also need to install `python-venv`. Depending on your OS, `pipx` should provide
installation instructions, if needed, during the following steps.

### Install `ifpd`

Run the following to install `ifpd`: `pipx install ifpd==2.1.1.post2` (note: this
tutorial was developed for `ifpd v2.1.1.post2`).

Check that the installation was successful by running `ifpd --version`, which should give
the following output: `/home/USERNAME/.local/bin/ifpd 2.1.1.post2`.

### Download and format oligonucleotide database

Let's continue by downloading the database:

```bash
cd $HOME
mkdir -p ifpd_static_content/db
wget http://ifish4u.com/custom/dbdownload/hg19 -O ifpd_static_content/hg19.bed.gz
```

Note: as the database is ~0.5 GB, this might take a few minutes, depending on your
connection speed.

Then, we need to format the database to be compatible with `ifpd`. We can do that with:

```bash
cd ifpd_static_content
gunzip hg19.bed.gz
ifpd mkdb hg19.bed hg19 --refGenome hg19 --output db/hg19 --increment-chrom-end
```

The formatting operation might take a few minutes, depending on your disk I/O speed.

Finally, let's check that everything is properly set up with:

```bash
ifpd dbchk db/hg19
```

An output of this sort indicates that everything is correct.

```
[11/23/21 08:01:32] INFO     Database name: "hg19"                                                                               dbchk.py:58
                    INFO     Reference genome: "hg19"                                                                            dbchk.py:61
                    INFO     Contains overlaps: False                                                                            dbchk.py:66
                    INFO     Oligo min distance: 11                                                                              dbchk.py:67
                    INFO     Oligo length range: (40, 40)                                                                        dbchk.py:68
                    INFO     Database checked.                                                                                   dbchk.py:69
                    INFO     Done. 👍 😃                                                                                         dbchk.py:71
```

## Tutorial

### 1. Start the GUI

Once the setup is finished, we can start the GUI with:

```bash
cd $HOME
ifpd serve ifpd_static_content
```

This command should give an output similar to:

```
Bottle v0.12.19 server starting up (using PasteServer())...
Listening on http://0.0.0.0:8080/
Hit Ctrl-C to quit.

serving on 0.0.0.0:8080 view at http://127.0.0.1:8080
```

Which means that the GUI should now be accessible at [0.0.0.0:8080](http://0.0.0.0:8080).

p.s., if your computer has a DNS name, you can specify it to the `ifpd serve` command
using the `-u` option.

### 2. Design a single probe for gene SOX4

1. Find the coordinates of the SOX4 gene via UCSC GenomeBrowser (accessible by clicking
    on the DNA helix).
2. Search for a probe of 96 oligos.
3. Allow for a 0.2 threshold during the first filter.
4. Request all candidate probes to be generated.
5. Use features in this order: size, homogeneity, centrality.
6. Submit.
7. Explore the output.
