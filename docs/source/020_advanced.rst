Advanced User Guide for Personal Modulefiles
============================================

This advanced guide is for users wishing to create modulefiles for their own software. The reasons are simple:

#. Install newer version of open sources software than is currently available.
#. Easily change version of applications or libraries under their own development.
#. Better documentation for what software is available.

You can create new version of some software and place it in your
personal PATH and forget about it. At least when it is in a module, it
be listed in the loaded modules it will also appear in the list of
available software via ``module avail``

User Created Modules
^^^^^^^^^^^^^^^^^^^^

Users can create their own modules. The first step is to add to the
module path: ::

   $ module use /path/to/personal/modulefiles

This will prepend ``/path/to/personal/modulefiles`` to the MODULEPATH
environment variable. This means that any modulefiles defined here
will be used instead of the system modules. 

Suppose that the user creates a directory called ``$HOME/modulefiles``
and he wants a personal copy of the "git" package and he does the
usual "tar, configure, make, make install" steps:  ::

    $ tar xf git-2.3.4.tar.bz2
    $ cd git-2.3.4
    $ ./configure --prefix=$HOME/pkg/git/2.3.4
    $ make
    $ make install

This document has assumed that 2.3.4 is the current version of git, it
will need to be replaced with the current version. To create a
modulefile for git one does: ::

    $ cd ~/modulefiles
    $ mkdir git
    $ cd git
    $ cat > 2.3.4.lua
    local home = os.getenv("HOME")
    local pkg = pathJoin(home,"pkg/git/2.3.4/bin")
    prepend_path("PATH", pkg)
    ^D  

This modulefile for git adds ``~/pkg/git/2.3.4/bin`` to the user's
path so that the personal version of git can be found. 

Starting first from the name: git/2.3.4.lua, Modulefiles with the .lua
extension are assumed to be written in lua and files without are
assumed to be written in TCL. 

The first line reads the user's HOME directory from the
environment. The second line uses the "pathJoin" function provided
from Lmod. It joins strings together with the appropriate number of
"/". The last line calls the "prepend_path" function to add the path
to git to the user's path. 

Finally the user can do: ::

   $ module load git
   $ type git
   ~/pkg/git/2.3.4/bin/git

For git to be available on future logins, users need to add the
following to their startup scripts or a saved collection.  ::

   $ module use $HOME/modulefiles
   $ module load git

The modulefiles can be stored in different directories. There is an
environment variable ``MODULEPATH`` which controls that. Modulefiles that
are listed in an earlier directory are found before ones in later
directories. This is similar to command searching in the ``PATH``
variable. There can be several versions of a command. The first one
found in the ``PATH`` is used.

Finding Modules With Same Name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Suppose the user has created a "git" module to provide the latest
available. At a later date, the system administrators add a newer
version of "git" ::

   $ module avail git
   --------------- /home/user/modulefiles ----------------
   git/2.3.4

   --------------- /opt/apps/modulefiles ----------------
   git/1.7.4   git/2.0.1   git/2.5.4 (D)

   $ module load git
 

The load command will load ``git/2.5.4`` because it is the highest
version.

If a user wishes to make their own version of git the default module
they will have to mark it as a default.  Marking a module as a default
is discuss in section XXX.
