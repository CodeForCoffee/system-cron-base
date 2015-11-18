	 	 	
system-cron-base
=========
Git -
This configuration starts with a Git workflow using permissions around the branching, pull request and merger functions. In this example we will use two primary groups.	
IT		
System Engineers that should be allowed to fork, branch and work with the system-cron-repo
Cyber		
CSSO (or similar) should have the ability to merge braches of the system  baseline repo	


An Example -
Marvin would like to make a change to the baseline sshd_config; he creates a branch for his changes and checks out his branch. He then can make his changes, commit them and uses the description in his pull request to reference a ticket or change control request.
After the pull request from Marvin, our Cyber Security official Tammi can approve and merge the changes into the repo for deployment.

**It is important that the SSH public key used by the service account be setup in Git as a “Deploy Key” with Read Only permissions; thus allowing user access to the repo.

System Cron -
In this example the script “system-cron-run” is deployed in my VM Template to the system and runs nightly under cron. As my example lacks LDAP/Kerberos I’ve created a local account that  ansible to runs as (svc_ansible); also this user has NOPASSWD sudo.

svc_ansible	ALL=(ALL) 	NOPASSWD: ALL

The script is broken into several functions
preFlight
	This prepares the system to run, sets up working directories, ssh keys, installs git and ansible.

nagiosRun
	Here a comparison is done against this run and the last successful run.  The files are stored in /tmp and the risk of removal of files is there; I felt it was a low risk given the interval that it’s run. Using a comparison on date the last successful run time is evaluated and output placed in a flat file for pickup. The plugin used to check this is deployed in this repo “check_simpleLogs” it’s an extremely simple check that looks within a file for a numerical output. This check was originally used to monitor output database jobs but has since been used for things like this. If the system returns that it's check was last run '1 day ago' or less the return code is 0 else a code of 3 is set to notify Nagios of a critical status.

runMe
	Where the ansible-pull is actually called.  At the top is a ‘cd /tmp’ and it’s not needed for it’s normal run; however as it’s run many times by hand I opted to place it there.
Ansible-pull uses a localhost inventory file and a working directory of “/opt/hardening”. The pull uses the standard git process; looking for any changes in the upstream repo and forcing them on the local system.


Role Name
=========
system-cron-base<br>
── roles<br>
│├── base<br>
│├── harden<br>
│└── nrpe<br>

Requirements
------------
This playbook was created to show a work flow that can be used in conjunction with system cron, git and Nagios. Using this as a guidline, one should be able to implement a system that allows for baseline enforcement of a security posture.
**Blindly running this playbook is certain to have an undesired effect on your system. **

Items in this playbook have been changed for public use.

Many items contained within these role were from numerous public projects. These any many more continue to show the possibilities with Ansible.

Just some references used -
Sam Doran
https://github.com/samdoran/ansible-role-rhel6stig

Major Hayden
https://major.io/2015/08/05/automated-testing-for-ansible-cis-playbook-on-rhelcentos-6/

Role Variables
--------------
No variables are required


Dependencies
------------
EPEL Repo
Ansible > 1.8

Example Playbook
----------------

No variables are required.
Author Information
------------------
Matt Wells - matt.wells@mosaic451.com
