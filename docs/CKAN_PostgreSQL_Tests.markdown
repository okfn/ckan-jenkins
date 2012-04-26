CKAN PostgreSQL Tests
=====================

The CKAN_PostgreSQL_Tests job works like this:

1. The Jenkins GitHub plugin maintains a clone of the CKAN GitHub repo in the
   Jenkins workspace for the CKAN_PostgreSQL_Tests job (the workspace is a
   directory at `/var/lib/jenkins/workspace/CKAN_PostgreSQL_Tests/`).

   The Jenkins GitHub plugin was installed from
   _Manage Jenkins -> Manage Plugins_ in the Jenkins web UI.

2. When a new commit is pushed to (any branch of) the CKAN GitHub repo, a push
   notification from GitHub triggers the Jenkins GitHub plugin. The plugin
   checks out the new commit and then triggers a new build of the
   CKAN_PostgreSQL_Tests job.

   The push notification was setup in the configuration of the
   CKAN_PostgreSQL_Tests job in the Jenkins web UI. You need to set:

   GitHub Project: https://github.com/okfn/ckan/  

   Source Code Management, Git, Repository URL: https://github.com/okfn/ckan.git  

   Source Code Management, Git, Branches to build: **  

   Build Triggers: Build when a change is pushed to GitHub

   You also need to configure the push notification on GitHub. This is in the
   Admin page of the CKAN GitHub project under Service Hooks. Set a
   Post-Receive URL. You can check what the post-receive URL should be in
   _Jenkins -> Manage Jenkins -> Configure System -> GitHub Web Hook_, also
   see the docs for the Jenkins GitHub plugin.

   The way the Jenkins GitHub plugin works is that when it receives a
   notification from GitHub it checks every branch of the repo that has not
   been fully merged into another branch, oldest branch first, and runs the
   Jenkins job on the first commit that it finds that Jenkins hasn't seen
   before.  

   This means that when you first set it up, it will see _every_ unmerged
   branch as new and each time it receives a notification it'll just test the
   oldest ubmerged branch, then on the next notification it'll test the next
   oldest and so on. The way I got past this was to configure the job to run
   on a timer every 20 minutes and leave it running for a couple of days
   until it had seen all the branches. Once it has seen each branch once, it
   will start finding the right branches when commits are pushed to GitHub.

3. The job executes a shell script, which you can see under
   _Build, Execute shell_ in the jobs' configuration in the Jenkins web UI. The
   shell script creates a new virtual environment in the job's workspace,
   activates the pyenv, installs CKAN and its dependencies into it, then runs
   the tests.

   Note that things CKAN needs outside of the virtualenv (the postgresql
   database, dependencies installed from apt, solr, etc.) have to be setup on
   the server manually first for the job to work. Just follow the CKAN install
   instructions.

4. To install CKAN and its dependencies the Jenkins script uses this virtualenv
   bootstrap script: <https://gist.github.com/2206132> which is essentially a
   scripted version of the virtualenv parts of the CKAN source install
   instructions. The Jenkins script git clones the bootstrap script from GitHub
   and then runs it.

5. The script runs nosetests with the `--with-xunit` option, which leaves a
   `nosetests.xml` file behind in the Jenkins workspace containing the test
   results.  This file is referenced in the Jenkins' job configuration under
   _Post-build Actions -> Publish JUnit test result report_. Jenkins reads the
   XML file and uses it to show nice-looking test results in the web UI.
