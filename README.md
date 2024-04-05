
			THE BASIC R README


	   (See "doc/FAQ" and "doc/RESOURCES" for more detailed information
				      - these files are only in the tarballs)
	   (See "INSTALL"             for help on installation)

1. INTRODUCTION

This directory contains the source code tree for R, which is a
language which is not entirely unlike (versions 3 and 4 of) the S
language developed at AT&T Bell Laboratories by Rick Becker, John
Chambers and Allan Wilks.

R is free software distributed under a GNU-style copyleft.

The core of R is an interpreted computer language with a syntax
superficially similar to C, but which is actually a "functional
programming language" with capabilities similar to Scheme.  The
language allows branching and looping as well as modular programming
using functions.  Most of the user-visible functions in R are written
in R, calling upon a smaller set of internal primitives.  It is
possible for the user to interface to procedures written in C or
Fortran languages for efficiency, and also to write additional
primitives.

The R distribution contains functionality for a large number of
statistical procedures.  Among these are: linear and generalized
linear models, nonlinear regression models, time series analysis,
classical parametric and nonparametric tests, clustering and
smoothing.  There is also a large set of functions which provide a
flexible graphical environment for creating various kinds of data
presentations.

A package specification allows the production of loadable modules for
specific purposes, and several thousand contributed packages are made
available through the CRAN sites (see
https://CRAN.R-project.org/mirrors.html for the current members).


2. HISTORY

R was initially written by Robert Gentleman and Ross Ihaka of the
Statistics Department of the University of Auckland.  In addition, a
large group of individuals has contributed to R by sending code and bug
reports.

Since mid-1997 there has been a core group who can modify the R source
code archive, listed in file doc/AUTHORS.

R 1.0.0 was released on 29 February 2000 and 2.0.0 on 4 October 2004.
R 3.0.0 came out on 3 April 2013.

This file has been minimally revised since the release of R 1.0.0.

3. PRESENT STATUS

The present version implements most of the functionality in the 1988
book "The New S Language" (the "Blue Book") and many of the
applications.  In addition, we have implemented a large part of the
functionality from the 1992 book "Statistical Models in S" (the "White
Book") and the 1998 book "Programming with Data" (the "Green Book").

All the R functions have been documented in the form of help pages in
an "output independent" form which can be used to create versions for
HTML, PDF, text etc.  A 1900+ page Reference Index (a collection of
most of the help pages: there is also a 3300+ page version with all
the help pages, including recommended packages) can be obtained in
PDF.  The manual `An Introduction to R' provides a more user-friendly
starting point, and there is an FAQ, a draft `R Language Definition'
manual and more specialized manuals on admininstration, data
import/export and extending R.  See INSTALL for instructions on how to
generate these documents.


4. GOALS

Our aim at the start of this project was to demonstrate that it was
possible to produce an S-like environment which did not suffer from
the memory-demands and performance problems which S has.  Somewhat
later, we started to turn R into a "real" system, but unfortunately we
lost a large part of the efficiency advantage in the process, so have
revised the memory management mechanism and implemented delayed
loading of R objects.  A lot of performance tuning has been done,
including the ability to use tuned linear-algebra libraries.

Longer-term goals include to explore new ideas: e.g. virtual objects
and component-based programming, and expanding the scope of existing
ones like formula-based interfaces.  Further, we wish to get a handle
on a general approach to graphical user interfaces (preferably with
cross-platform portability), and to develop better 3-D and dynamic
graphics.


Sincerely,
The R Core Team.



# FOR THE OPENCLINICA TEAM

This R distribution of 4.3.0 is modified to work with AWS Lambda Amazon
Linux 2 environment. If it needs to be rebuilt (and this applies to other
versions of R) this source code must be downloaded into the /opt/R
folder of an Amazon Linux 2 instance (currently named Zique_Dev-2(linux2)
in the dev AWS account). The following is the instructions on how to 
complete the building process.

```
yum groupinstall "Development Tools"
yum install bzip2 bzip2-devel
yum install pcre2 pcre2-devel
yum -y install libxml2-devel openssl-devel java-1.8.0-openjdk 
yum -y install curl libcurl libcurl-devel
cd /opt/R
./configure --with-readline=no --with-x=no
make
/opt/R/bin/Rscript -e "install.packages(c(\"aws.s3\",\"askpass\", \"aws.signature\",\"base64enc\",\"httr\",\"jsonlite\",\"logging\",\"xml2\",\"openssl\",\"aws.lambda\"), repos=\"http://cran.r-project.org\")"
```

## Optional command if specific libraries for R are needed
```
/opt/R/bin/Rscript -e "install.packages(c(\"dplyr\",\"stringr\", \"gtools\",\"tibble\"), repos=\"http://cran.r-project.org\")"\n
```
NOTE: The optional libraries and their dependencies must be packaged in their own /R/library folder and zipped to be used as a separate AWS Lambda layer. The structure of the directory must be /R/library when zipped. 

## Preparation for distributing for AWS Lambda
```
cp -r /opt/R/bin /opt/R/doc /opt/R/etc /opt/R/lib /opt/R/library /opt/R/modules /opt/R/share /opt/R/include /root/Builds/R-4.3.0/R/
cp /root/Builds/base-files/bootstrap /root/Builds/base-files/bootstrap.R /root/Builds/base-files/runtime.R /root/Builds/R-4.3.0/
cp /usr/bin/which /root/Builds/R-4.3.0/R/lib/which
cp /usr/lib64/libgfortran.so.4.0.0 /root/Builds/R-4.3.0/R/lib/libgfortran.so.4
cp /usr/lib64/libgomp.so.1.0.0 /root/Builds/R-4.3.0/R/lib/libgomp.so.1
cp /usr/lib64/libquadmath.so.0.0.0 /root/Builds/R-4.3.0/R/lib/libquadmath.so.0
cp /usr/lib64/libstdc++.so.6.0.24 /root/Builds/R-4.3.0/R/lib/libstdc++.so.6
cd /root/Builds/R-4.3.0
zip -r R-4.3.0-base.zip ./*
aws s3 cp /root/Builds/R-4.3.0/R-4.3.0-base.zip s3://zique-lambda-runtimes/R-4.3.0/R-4.3.0-base.zip
```