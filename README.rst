Jenkins Job Backups
===================

This is an archive and history of the following jobs on the CKAN jenkins server:

* Backup_Jobs (this job)
* CKAN_PostgresSQL_Tests
* Package_CKAN

To add another job to the backup, edit the build script in *Backup_Jobs*.  See
*docs/Backup_Jobs.rst* for more details.

Please **do not**:

* Make changes to the *jenkins-archive* branch of this repository.  This branch
  is reserved for the use of jenkins job.

* Make manual changes to any files under ``./jobs``.  Again, these files are
  the archived jenkins config files, and any changes should be recorded using
  the *Backup_Jobs* task.

It *is* however ok to periodically merge in changes from the *jenkins-archive* branch into
master.
