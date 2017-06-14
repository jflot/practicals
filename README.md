# Practicals
A set of practicals for teaching genomics during the 2017 ICME course.

For discussion of the various technical problems that will certainly arise during the practical: https://etherpad.wikimedia.org/p/8ICME.

## Opening a terminal (= command-line environment)
If your operating system is Linux or OSX, you just need to click on the Terminal icon (for OSX users, the Terminal program is hidden in Applications/Utilities). If your operating system is Windows, you should install Cygwin (https://www.cygwin.com/). Cygwin is a wonderful tool that turns a Windows computer into a real bioinformatic platform, by giving you a proper terminal in which you can use the exact same commands as on a Linux computer. If your operating system is Windows 10, you have actually the choice between installing Cygwin or installing the new Windows Subsystem for Linux (WSL): https://msdn.microsoft.com/fr-fr/commandline/wsl/about; here are the installation instructions: https://msdn.microsoft.com/en-us/commandline/wsl/install_guide. In both cases, you will end up with a Linux-like terminal that you should open (usually by clicking on the Cygwin or WSL icon).

## Making sure you have a working X11 server in your computer
In your terminal window, type `xeyes`: you should see some rolling eyes appearing in a small window on your screen (you can then close this window by clicking on the cross in the corner of the window). If it does not work, there can be three problems (or a combination of them): you may have no installed X11 server on your computer; your installed X11 server may require to be started manually before typing `xeyes`; and/or you may need to set the $DISPLAY variable in your command-line environment.

### Installing a X11 server
If your operating system is Linux, then you have already a X11 server for sure. If your operating system is OSX, you should install one (I use XQuartz, which is straightforward to install: just download the DMG archive from https://www.xquartz.org/). If your operating system is Windows, you can install Xming (https://sourceforge.net/projects/xming/files/Xming/6.9.0.31/Xming-6-9-0-31-setup.exe/download) or Cygwin/X (https://x.cygwin.com/) or VcXsrv Windows X Server (https://sourceforge.net/projects/vcxsrv); if you install Cygwin and select the proper package during the setup step, then Cygwin/X gets installed at the same time (if you installed Cygwin already but forgot to include Cygwin/X, you can just run the Cygwin setup program again and add Cygwin/X to your selected packages).
If you choose to install VcXsrv Windows X Server, you can find detailed instructions in https://www.slothparadise.com/setup-bash-windows-10-developers-ultimate-setup/ ("4. Install VcXsrv Windows X Server for opening GUI applications").

### Launching the X11 server
Here it is simply a matter of clicking on the X11 server icon (either XMing or Cygwin/X or VcXsrv Windows X Server) so that it starts.

### Setting the $DISPLAY variable
For that you need to enter the following command `export DISPLAY=:0` in your terminal on your computer. If you want this to be done automatically you just need to add this line to the .bashrc file in your home directory (or create a .bashrc file containing this line if there is none yet): while in your home directory (to go there, just type `cd` without any further path and you will return directly to your own directory), type `echo 'export DISPLAY=:0' >> .bashrc` then `source .bashrc` to apply the new settings to your current session).

## Logging into the cluster environment
Log in to your account on the CINECA cluster by typing `ssh -X a08trb##@login.pico.cineca.it` in your terminal, where ## is the account number you were attributed (if `-X`does not work, try with `-Y`, which is another, more secure connection option). The `-X` or `-Y` in the `ssh` command is important to allow forwarding graphical instructions from the CINECA cluster to your computer, where it will be displayed: to test whether it works, try typing `xeyes` in the command-line environment and the rolling eyes should appear on your home computer. If it does not work but `xeyes` worked when you were typing it in your local terminal (i.e., before logging into the CINECA cluster) then it means that you need to fix the SSH settings. To do so, type `exit` to close your connection to the CINECA cluster, return to your home directory on your local computer (`cd`), create there a folder called .ssh (`mkdir .ssh`) if it does not already exists then go into this folder (`cd .ssh`). Create a file called "config" containing three lines (or add these three lines at the bottom of the file if it already exists) using the command `echo "Host *" >> config; echo "    ForwardAgent yes" >> config; echo "    ForwardX11 yes" >> config`. Finally, change the modes (permissions) on the config file by typing `chmod 600 config` (see https://en.wikipedia.org/wiki/File_system_permissions and https://en.wikipedia.org/wiki/Modes_(Unix), `chmod` simply means "change mode"). This should have solved the problem, so you can now log back into your account on the CINECA cluster and check that `xeyes` works.

## Navigating your home and scratch directories
You are now at the root of your home account (you can see your current path using `pwd`, and list the content of your current directory using `ls` ou `ls -rtclh` for more details).
When you type `xeyes` or any other command, the system automatically looks for the corresponding program in the folders listed in your $PATH variable (to see the content of this variable, try `echo $PATH`). To copy a file, use the command `cp`: `cp /usr/bin/xeyes ~/bin/` will copy the program xeyes into the bin folder (named because it will contain binaries, i.e., programs) of your home directory. Since this directory is part of your $PATH, any program put into this folder will be launchable from anywhere in your system, just by typing its name. If it does not work, it usually means that the program is not authorized for execution: in this case, you can change its permissions using `chmod a+x` followed by the name of the file.

You will be working in your scratch directory and not in your home directory. The path to your scratch directory is stored in the variable $CINECA_SCRATCH: to see it, use the command `echo $CINECA_SCRATCH`; to go there, use `cd $CINECA_SCRATCH` (`cd` simply means "change directory").


## Installing the programs required for the practical
Another version useful command is `cat` (which means "concatenate"). You can use it to display the content of a file (`cat [name of the file to display]`) or of two files one after the one (`cat [filename1] [filename2]`), as well as to concatenate the contents of two files and save it into a third file (`cat [filename1] [filename2] > [new file]`).

Copy/paste the following script into your CINECA terminal (while in $CINECA_SCRATCH):
```#! /bin/bash
echo 'module load profile/advanced' >> ~/.bashrc
echo 'module load autoload zlib' >> ~/.bashrc
echo 'module load git' >> ~/.bashrc
echo 'module load idba-ud' >> ~/.bashrc

source ~/.bashrc

echo "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"

echo -e "\n This script will install the dependencies necessary for the
assembly practical.\n"

echo -e "PLEASE NOTE THAT THIS SCRIPT WAS WRITTEN IN APRIL 2017\n"
echo -e "To install more recent versions of the various programs, please edit the script\n"

echo "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++\n"

#================== SETTIN ENV  ===================
echo -e "\n Creating a deps (dependencies) folder in the working directory ($(pwd))"

wd=$(pwd)

DEPS=$(pwd)/deps
mkdir -p $DEPS

cd $DEPS

echo -e "\n Creating a folder that will contain sl to binaries \n"

SLBIN=$DEPS/slbin
echo $SLBIN
mkdir -p $SLBIN

echo -e "\n Creating a folder that will contain the tar archives \n"

TAR=$DEPS/archives
echo $TAR
mkdir -p $TAR

echo -e "================== Jellyfish 2.2.6 ===================\n"
echo -e "Installing the following software: Jellyfish"
module load zlib/1.2.8--gnu--4.8.3
wget -O jellyfish-2.2.6.tar.gz https://github.com/gmarcais/Jellyfish/releases/download/v2.2.6/jellyfish-2.2.6.tar.gz

tar -zxvf jellyfish-2.2.6.tar.gz
cd jellyfish-2.2.6

./configure
make

ln -s $(pwd)/bin/jellyfish $SLBIN/jellyfish

cd $DEPS

mv jellyfish-2.2.6.tar.gz $TAR

echo -e "================== Quast 4.5 ===================\n"
echo -e "Installing the following software: Quast"

wget -O quast.tar.gz https://github.com/ablab/quast/archive/quast_4.5.tar.gz
tar -zxvf quast.tar.gz
cd quast-quast_4.5

./install.sh

ln -s $(pwd)/quast.py  $SLBIN/quast

cd $DEPS 

mv quast.tar.gz $TAR

echo -e "================== Bloocoo ===================\n"
echo -e "Installing the following software: Bloocoo"

wget -O Bloocoo.tar.gz https://github.com/GATB/bloocoo/releases/download/v1.0.7/Bloocoo-v1.0.7-bin-Linux.tar.gz
tar -zxvf Bloocoo.tar.gz

cd $DEPS/Bloocoo-v1.0.7-bin-Linux/bin

ln -s $(pwd)/Bloocoo $SLBIN/Bloocoo

cd $DEPS

mv Bloocoo.tar.gz $TAR


echo -e  "================== xmgrace ===================\n"
echo -e "Installing the following software: xmgrace"
wget ftp://ftp.fu-berlin.de/unix/graphics/grace/src/grace-latest.tar.gz
tar -zxvf grace-latest.tar.gz

cd grace-5.1.25/

./configure --prefix=$(pwd)

make
make install

ln -s $(pwd)/grace/bin/xmgrace $SLBIN/xmgrace
cd $DEPS
echo -e "================== FINISH! ===================\n"

echo 'export PATH=$CINECA_SCRATCH/deps/slbin:$PATH' >> ~/.bashrc
source ~/.bashrc

echo -e "Now you have all the dependencies to run on PICO"

```

Once the installation is complete, you can try launching the various programs you just installed by typing their names in the command line: `Bloocoo`, `jellyfish`, `idba_ud`, `xmgrace`. If you get any error message, let us know.


## Starting an interactive session on a computing node
If everybody works on the login node, it may slow down the sytem. To avoid this, start an interactive session on a computing node: `qsub -A train_metage17 - X -I -l select=1:ncpus=4:mem=24GB -l walltime=2:00:00 -N 8icme_day1 -q R712694 -W group_list=train_metage17` (if it does not work, try without the queue name: `qsub -I -l select=1 -X -A train_metage17`). Check that X11 forwarding works also in the interactive session using `xeyes`. If this works, it means that the graphical instructions responsible for these rolling eyes are generated by the computing node then forwarded to the login node, which forwards them to you terminal environment, which sends them to your local X11 server, which finally displays them on your screen. If X11 forwarding is not working for some reason, you may have to remove the '-X' from the above command line.

## Downloading an _Escherichia coli_ reference sequence
Go to $CINECA_SCRATCH and create a folder called "AssemblyPractical", then enter it.
Download the sequence of E. coli strain Sakai: `wget http://seqphase.mpg.de/Sakai.fasta`. It is now in your folder, and you can look at the first 10 lines of it using `head Sakai.fasta` (to look at the first 100 lines, try `head -100 Sakai.fasta`). To find out the number of lines of Sakai.fasta, try `wc -l Sakai.fasta` (`wc` means "word count", and with -l it actually counts lines rather than words). For our practical to proceed faster, we will only use the first 10% of the genome: `head -7886 Sakai.fasta > toy.fasta'.

## Simulating reads
In this first analysis, we will analyse the effect of read coverage on the quality of a genome assembly. To do so, let's start by simulating read sets of know coverage using the `sim_reads` tool (part of the IDBA_UD package):
```
sim_reads --paired --depth 1 toy.fasta  toyD1.fasta
sim_reads --paired --depth 2 toy.fasta  toyD2.fasta
sim_reads --paired --depth 3 toy.fasta  toyD3.fasta
sim_reads --paired --depth 4 toy.fasta  toyD4.fasta
sim_reads --paired --depth 5 toy.fasta  toyD5.fasta
sim_reads --paired --depth 6 toy.fasta  toyD6.fasta
sim_reads --paired --depth 7 toy.fasta  toyD7.fasta
sim_reads --paired --depth 8 toy.fasta  toyD8.fasta
sim_reads --paired --depth 9 toy.fasta  toyD9.fasta
sim_reads --paired --depth 10 toy.fasta  toyD10.fasta
sim_reads --paired --depth 15 toy.fasta  toyD15.fasta
sim_reads --paired --depth 20 toy.fasta  toyD20.fasta
sim_reads --paired --depth 25 toy.fasta  toyD25.fasta
sim_reads --paired --depth 30 toy.fasta  toyD30.fasta
sim_reads --paired --depth 40 toy.fasta  toyD40.fasta
sim_reads --paired --depth 50 toy.fasta  toyD50.fasta
sim_reads --paired --depth 60 toy.fasta  toyD60.fasta
sim_reads --paired --depth 70 toy.fasta  toyD70.fasta
sim_reads --paired --depth 80 toy.fasta  toyD80.fasta
sim_reads --paired --depth 90 toy.fasta  toyD90.fasta
sim_reads --paired --depth 100 toy.fasta toyD100.fasta
```
We could also have used the shorter but more complex command: `for i in 1 2 3 4 5 6 7 8 9 10 15 20 25 30 40 50 60 70 80 90 100; do sim_reads --paired --depth $i toy.fasta toyD$i.fasta; done`, which gives exactly the same result.

Now, let's assemble each dataset using IDBA_UD: `for i in 1 2 3 4 5 6 7 8 9 10 15 20 25 30 40 50 60 70 80 90 100; do echo $i; idba_ud -r toyD$i.fasta --no_correct -o toyD$i; done`.

To analyse the result, let's run raw_N50 on each final contig set: `for i in 1 2 3 4 5 6 7 8 9 10 15 20 25 30 40 50 60 70 80 90 100; do raw_n50 toyD$i/contig.fa;done > coverage_effect.txt`

Let us plot the result using `xmgrace`. First we need to make a 2-column file. The first column is obtained easily using `for i in 1 2 3 4 5 6 7 8 9 10 15 20 25 30 40 50 60 70 80 90 100; do echo $i; done > coverage.col`. The second colum can be extracted from the coverage_effect.txt file using the command awk: for instance, `awk '{print $2}' coverage_effect.txt > nbcontigs.col`. You can then combine the two columns and plot the result using `paste coverage.col nbcontigs.col |xmgrace -`. What do you observe?
Let's look at the N50s: `awk '{print $4}' coverage_effect.txt > n50s.col`, then `paste coverage.col n50.col |xmgrace -`. You can also plot other variables in the same way. Why do you think is happening, and how could we obtain a better genome assembly?

## Analysing the repeat structure of a genome
Let's use `jellyfish` to count the occurence of all the kmers in our toy sequence. For example, to generate kmer counts for k=22, you can run `jellyfish count -m 22 -c 3 -s 10000000 -o toyk22  toy.fasta` then jellyfish histo -f toyk22 > histok22.txt` (or better, `jellyfish histo -f toyk22 | awk '$2!=0'> histok22.txt` to see only the counts >0). What can you say? Try now with higher k values. Does it help you understand the problem we are facing?

## Further on
Try to craft some hypothesis of which parameter(s) you think should be improved to obtained a perfect assembly of this toy genome, then test this hypothesis using the simulation approach outlined above.








