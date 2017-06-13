# Practicals
A set of practicals for teaching genomics during the 2017 ICME course.

For discussion of the various technical problems that will certainly arise during the practical: https://etherpad.wikimedia.org/p/8ICME.

## Opening a terminal (= command-line environment)
If your operating system is Linux or OSX, you just need to click on the Terminal icon (for OSX users, the Terminal program is hidden in Applications/Utilities). If your operating system is Windows, your best choice is to install Cygwin (https://www.cygwin.com/). Cygwin is a wonderful tool that turns a Windows computer into a real bioinformatic platform, by giving you a proper terminal in which you can use the exact same commands as on a Linux computer.

## Logging into the cluster environment
Log in to your account on the CINECA cluster using `ssh -X a08trb##@login.pico.cineca.it`, where ## is the account number you were attributed (if `-X`does not work, try with `-Y` which is more secure). The `-X` in the `ssh` command is important to allow forwarding graphical information to your terminal: to test whether it works, try typing `xeyes` in the command-line environment: you should see some rolling eyes appearing in a small window on your screen (you can then close this window by clicking on the cross in the corner of the window).

## Navigating your home and scratch directories
You are now at the root of your home account (you can see your current path using `pwd`, and list the content of your current directory using `ls` ou `ls -rtclh` for more details).

When you type `xeyes` or any other command, the system automatically looks for the corresponding program in the folders listed in your $PATH variable (to see the content of this variable, try `echo $PATH`). To copy a file, use the command `cp`: `cp /usr/bin/xeyes ~/bin/` will copy the program xeyes into the bin folder (named because it will contain binaries, i.e., programs) of your home directory. Since this directory is part of your $PATH, any program put into this folder will be launchable from anywhere in your system, just by typing its name. If it does not work, it usually means that the program is not authorized for execution: in this case, you can change its permissions using `chmod a+x` followed by the name of the file, see https://en.wikipedia.org/wiki/File_system_permissions and https://en.wikipedia.org/wiki/Modes_(Unix) (`chmod` simply means "change mode").

You will be working in your scratch directory and not in your home directory. The path to your scratch directory is stored in the variable $CINECA_SCRATCH: to see it, use the command `echo $CINECA_SCRATCH`; to go there, use `cd $CINECA_SCRATCH` (`cd` simply means "change directory").


## Installing the programs required for the practical
Another version useful command is `cat` (which means "concatenate"). You can use it to display the content of a file (`cat [name of the file to display]`) or of two files one after the one (`cat [filename1] [filename2]`), as well as to concatenate the contents of two files and save it into a third file (`cat [filename1] [filename2] > [new file]`).

Use the `cat > build_deps.sh` command to create a file called _build_deps.sh_ and open it for writing. Then copy/paste the following script into that file:
```#! /bin/bash
module load profile/advanced
module load autoload zlib
module load git
module load idba-ud

echo "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"

echo -e "\n This script will install the dependencies necessary for the
assembly practical.\n"

echo -e "PLEASE NOTE THAT THIS SCRIPT WAS WRITTEN IN APRIL 2017\n"
echo -e "To install more recent version, please edit the script\n"

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

echo -e "\n Creating a folder that will contain the tar arichives \n"

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

echo -e "================== Bcalm ===================\n"
echo -e "Installing the following software: Bcalm"

git clone https://github.com/Malfoy/BCALM.git
cd BCALM

make

ln -s $(pwd)/bcalm $SLBIN/bcalm

cd $DEPS

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

export PATH=$CINECA_SCRATCH/deps/slbin:$PATH

echo -e "Now you have all the dependencies to run on PICO"
```

Press Ctr^D to close the file and record the changes, then launch the installation script using `source build_deps.sh`.

Once it is done, you can try launching the various programs we just installed by typing their names in the command line: `bcalm`, `bcool`, `jellyfish`, `idba_ud`, `xmgrace`. If you get any error message, let us know.

## Starting an interactive session on a cluster node

If everybody works on the login node, it may slow down the sytem.
To avoid this, start an interactive session on a cluster nodd: `qsub -I -l select=1 -X -A train_metage17 -q R712694  -W group_list=train_metage17` (if it does not work, try without the queue name: `qsub -I -l select=1 -X -A train_metage17qsub -I -l select=1`). Check that X11 forwarding works also in the interactive session using `xeyes`).
