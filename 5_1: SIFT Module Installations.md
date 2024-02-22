# Module Installation Non Root
Before the implementation, there are several perl modules that have to be installed including DBI, BioPerl, LWP, and Switch.pm, and SIFT4G of course. Installation of all modules may take a while and is the hardest part of this process; be prepared to cry. 

## Installing GCC
gcc may be already installed, but I was getting so many errors indicating that it wasn't so here we go: 
```bash
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I
conda install -c conda-forge gcc
```

## Installing SIFT4G
https://github.com/rvaser/sift4g
To install sift4g, you need the most updated version of gcc. Even though I had clearly installed it (previous step), I had to manually call gcc otherwise it was defaulting to an 8 year old version of gcc and the compilation kept failing🤢😭

```bash
qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I

#Call most updated version of gcc.
module load gcc/9.3.1

#Make a Directory for sift4g
mkdir BuildSIFT
cd ~/SzpiechLab/abc6435/WarblerROH/SIFT4G/BuildSIFT/

#Installing sift4g
git clone --recursive https://github.com/rvaser/sift4g.git sift4g
cd sift4g/
make

#Verifying sift4g installation
ls bin
#sift4g should should show up (for me it shows up in green which indicates that it is an executable file.) Another way to verify is to do: 
file sift4g
#[output] sift4g: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), dynamically linked (uses shared libs), BuildID[sha1]=03d91ee11887a6505e3abd30d66b77db6714eb05, for GNU/Linux 2.6.32, not stripped
```
✎Note✎: Make sure to write down the pathway to your sift program. You will need this later in Step1d when you write your configuration files. For me, the pathway is `SIFT4G_PATH=~/SzpiechLab/abc6435/WarblerROH/SIFT4G/BuildSIFT/sift4g/bin/sift4g`

## Installing Perl Modules
**Helpful Threads for installing perl modules:** 
https://stackoverflow.com/questions/20568836/cant-locate-dbi-pm
https://www.popmartian.com/tipsntricks/2011/04/11/how-to-pass-perl-library-paths-from-the-environment/
https://www.phys.ufl.edu/~prescott/perl_modules_normal_user.html
https://web.archive.org/web/20211206030908/http://alumni.soe.ucsc.edu/~you/notes/perl-module-install.html
https://code.google.com/archive/p/biotoolbox/wikis/BioToolBoxSetUp.wiki
https://stackoverflow.com/questions/32726324/i-installed-a-module-successfully-with-cpan-but-perl-cant-find-it-why

Be on a node when you do the installations because according to Zach, weird things happen if you try to do this on the head node. 
`qsub -A zps5164_a_g_hc_default -l feature=rhel7 -l walltime=10:00:00 -l nodes=1:ppn=1 -l mem=100gb -I` 

I don't know if it's necessary but just call gcc also before you install... just in case. 
`module load gcc/9.3.1`

There are two ways to install perl modules that I found to be effective. You can do it using -MCPAN or by downloading the tar file using wget. Both ways worked for me. In either case, you will have to specify where you want these modules installed using a PREFIX or INSTALL_BASE. You'll see. 

### Installing DBI using -MCPAN
```bash
perl -MCPAN -Mlocal::lib -e 'CPAN::install(DBI)'
cp ~/.cpan/sources/authors/id/T/TI/TIMB/DBI-1.643.tar ~/perl5/lib/perl5
cd ~/perl5/lib/perl5
tar -xvzf DBI-1.643.tar
cd DBI-1.643
perl Makefile.PL  PREFIX=~/perl5/lib/perl5
make  
make test  
make install  
```

### Installing LWP using -MCPAN
```bash
perl -MCPAN -Mlocal::lib -e 'CPAN::install(LWP)'
cp ~/.cpan/sources/authors/id/O/OA/OALDERS/libwww-perl-6.67.tar.gz ~/perl5/lib/perl5
cd ~/perl5/lib/perl5
tar -xvzf libwww-perl-6.67.tar.gz
cd libwww-perl-6.67
perl Makefile.PL PREFIX=~/perl5/lib/perl5
make  
make test  
make install
```

### Installing Switch using WGET
```bash
cd ~/perl5/lib/perl5
wget https://cpan.metacpan.org/authors/id/C/CH/CHORNY/Switch-2.17.tar.gz
tar -xvzf Switch-2.17.tar.gz
cd Switch-2.17
perl Makefile.PL INSTALL_BASE=~/perl5/lib/perl5
make  
make test  
make install
```

### Installing BioPerl using WGET
```bash
cd ~/perl5/lib/perl5
wget https://cpan.metacpan.org/authors/id/C/CJ/CJFIELDS/BioPerl-1.7.8.tar.gz
tar -xvzf BioPerl-1.7.8.tar.gz
cd BioPerl-1.7.8
perl Makefile.PL INSTALL_BASE=~/perl5/lib/perl5
make  
make test  
```

## Specifying Pathway to Modules
Because we installed this without administrative privileges (non root), we have to specify the path to the modules so that the computer knows where to look. Do this by adding a PERL5LIB line to the end of your bashrc file.  
```bash
echo `export PERL5LIB=/storage/home/abc6435/perl5/lib/perl5:$PERL5LIB` >> ~/.bahsrc
```


