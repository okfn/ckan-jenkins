Backup_Jobs Jenkins Task
========================

This jenkins job is used to archive the conifguration files (and hence scripts)
of the jobs stored on jenkins.  Each job maintains a ``config.xml`` file which
stores all the data about the job, including any scripts defined in the job.
This jenkins job copies those ``config.xml`` files into a local git repository,
and then pushes any changes to ``git@github.com:okfn/ckan-jenkins.git``.

Automated Running
-----------------

The job is setup to run automatically every hour without supervision.  Only
chanegs to the ``config.xml`` files will result in a new commit.

Manual Running
--------------

The job can also be run manually if you wish:

1.  Log on to jenkins: http://s031.okserver.org:8080/login
#.  Visit the homepage: http://s031.okserver.org:8080
#.  Select **Backup_Jobs** from the main table.
#.  Select **Build Now** from the menu on the left.
#.  Fill in the form with the following info:

commit_message
  This is the commit message to include with the commit.

Archiving
---------

This job is setup to commit changes to the **jenkins-archive** branch.  The
idea is that no other person commits to this branch.  And so avoids any merge
issues that may occur from the script running unsupervised.

This branch can be merged into **master** for completeness.

Adding Jobs To The Archive
--------------------------

By deafult, only jobs named in the jenkins script are archived.  To add other
existing jenkins jobs to the archive, add it to the shell script in the
*Backup_Jobs* task:

1.  Log on to jenkins: http://s031.okserver.org:8080/login
#.  Visit the homepage: http://s031.okserver.org:8080
#.  Select **Package CKAN** from the main table.
#.  Select **Configure** from the menu on the left.
#.  Edit the build script, and save.

