# practicals
A set of practicals for teaching genomics

Use the `cat > build_deps.sh` command to create a file called _build_deps.sh_ and open it for writing. Then copy/paste the following script into that file:
```#! /bin/bash
module load profile/advanced
module load autoload zlib
module load git
module load idba-ud

echo "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++"

echo -e "\n This script will install the dependencies necessary for the
metagenomic pipeline.\n"

echo -e "PLEASE NOTE THAT THIS SCRIPT HAS BEEN WRITTEN IN APRIL 2017\n"
echo -e "To install more recent version, pleas edit the script\n"

echo "+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++\n"

#================== SETTIN ENV  ===================
echo -e "\n Creating a deps folder in the working directory ($(pwd))"

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
echo -e "I'm going to install the following software: Jellyfish"
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
echo -e "I'm going to install the following software: Quast"

wget -O quast.tar.gz https://github.com/ablab/quast/archive/quast_4.5.tar.gz
tar -zxvf quast.tar.gz
cd quast-quast_4.5

./install.sh

ln -s $(pwd)/quast.py  $SLBIN/quast

cd $DEPS 

mv quast.tar.gz $TAR

echo -e "================== Bloocoo ===================\n"
echo -e "I'm going to install the following software: Bloocoo"

wget -O Bloocoo.tar.gz https://github.com/GATB/bloocoo/releases/download/v1.0.7/Bloocoo-v1.0.7-bin-Linux.tar.gz
tar -zxvf Bloocoo.tar.gz

cd $DEPS/Bloocoo-v1.0.7-bin-Linux/bin

ln -s $(pwd)/Bloocoo $SLBIN/Bloocoo

cd $DEPS

mv Bloocoo.tar.gz $TAR

echo -e "================== Bcalm ===================\n"
echo -e "I'm going to install the following software: Bcalm"

git clone https://github.com/Malfoy/BCALM.git
cd BCALM

make

ln -s $(pwd)/bcalm $SLBIN/bcalm

cd $DEPS

echo -e  "================== xmgrace ===================\n"
echo -e "I'm going to install the following software: xmgrace"
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

Press _Ctr^D_ to close the file and record the changes, then `chmod 777 build_deps.sh` to make it executable. Finally, launch the installation script using `./build_deps.sh`.
