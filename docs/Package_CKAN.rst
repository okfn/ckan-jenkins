Packaging CKAN
==============

This document outlines the process to build and distribute the CKAN debian
packages.  It also outlines the technical details of the jenkins task used; and
help on installing buildkit on a new server.

Process
-------

To package and distribute (ie - host on apt.ckan.org), follow the process
outlined below:

1.  Log on to jenkins: http://s031.okserver.org:8080/login
#.  Visit the homepage: http://s031.okserver.org:8080
#.  Select **Package CKAN** from the main table.
#.  Select **Build Now** from the menu on the left.
#.  Fill in the form with the following info:

branch_name
  The name of the git branch within the CKAN repo to package.  eg ``master`` or
  ``release-1.6.1``

repo_name
  The name to give the vesion of CKAN you're packaging, eg -
  ``ckan-current-dev`` or ``ckan-1.6.1`` or ``ckan-1.6.1-rc``.

package_version
  Use this to bump the version number of a build that is being packaged to 
  ``repo_name`` that already exists.  ie - when packaging ``ckan-1.6.1`` for
  the first time, then ``0001`` is fine.  When packaging ``ckan-1.6.1`` the next
  time, increment this value to ``0002``, and so on.

  It's perfectly fine to leave this blank, in which case the (auto-incrementing)
  jenkins build number is used instead.

#.  The console output can be observed for any errors.
#.  Once completed, the built packages will be available on
    ``http://apt.ckan.org.s3.amazonaws.com/{repo_name}``.

Jenkins Task
------------

The jenkins task:

1.  Clones the given CKAN branch.
#.  Runs the ``build.sh`` script available from the above checkout.  This
    script uses ``buildkit``, which must be available on the server.
#.  The built packages are then copied into a local apt repository structure.
#.  The local repository is then synced to s3 using ``s3cmd``.

Installing Buildkit
-------------------

The build script depends upon buildkit being available.  I found that I needed
to install this from source, as the http://apt.3aims.com/ apt repository no
longer hosts a version for lucid.

Installation
............

1.  Download and untar the source from http://pypi.python.org/packages/source/b/buildkit/buildkit-0.2.2.tar.gz#md5=e048f06414470101bf4a433398144255
#.  Install the necessary buildtools for making debian packages: ::

  sudo apt-get install dh_make devscripts cdbs reprepro

#.  Build buildkit (loosely follows instructions on http://packages.python.org/buildkit/manual.html#buildkit-tutorial
    ignoring the VM stuff): ::

  PACKAGEVERSION=01
  mkdir -p dist/buildkit
  python -m buildkit.run pkg nonpython -p "$PACKAGEVERSION" -o dist/buildkit --deb buildkit_deb
  python -m buildkit.run pkg python -p "$PACKAGEVERSION" -o dist/buildkit --author-email james@pythonweb.org --deb .

#.  You should have two ``.deb`` files in ``dist/buildkit``, which you can
    then install: ::

  sudo dpkg -i dist/buildkit/*.deb

#.  Check buildkit is installed: ::

  buildkit --help
    
