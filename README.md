sa-gitlab-ce
============

Standalone gitlab installation according to manual instructions provided via https://gitlab.com/gitlab-org/gitlab-ce/blob/master/doc/install/installation.md

[![Build Status](https://travis-ci.org/softasap/sa-gitlab-ce.svg?branch=master)](https://travis-ci.org/softasap/sa-gitlab-ce)

Simple

  roles:
    - {
        role: "sa-gitlab-ce"
      }


Advanced:


  roles:
    - {
        role: "sa-gitlab-ce"
      }

# Troubleshouting

Running  ~/gitlab$ bundle exec rake gitlab:check RAILS_ENV=production
might gice you a clue what is wrong

<pre>
git@BOX:~/gitlab$ bundle exec rake gitlab:check RAILS_ENV=production
Checking GitLab Shell ...

GitLab Shell version >= 2.7.2 ? ... OK (2.7.2)
Repo base directory exists? ... yes
Repo base directory is a symlink? ... no
Repo base owned by git:git? ... yes
Repo base access is drwxrws---? ... yes
hooks directories in repos are links: ... can't check, you have no projects
Running /home/git/gitlab-shell/bin/check
Check GitLab API access: OK
Check directories and files:
	/home/git/repositories/: OK
	/home/git/.ssh/authorized_keys: OK
Test redis-cli executable: redis-cli 2.8.4
Send ping to redis server: PONG
gitlab-shell self-check successful

Checking GitLab Shell ... Finished

Checking Sidekiq ...

Running? ... yes
Number of Sidekiq processes ... 1

Checking Sidekiq ... Finished

Checking Reply by email ...

Reply by email is disabled in config/gitlab.yml

Checking Reply by email ... Finished

Checking LDAP ...

LDAP is disabled in config/gitlab.yml

Checking LDAP ... Finished

Checking GitLab ...

Git configured with autocrlf=input? ... yes
Database config exists? ... yes
All migrations up? ... yes
Database contains orphaned GroupMembers? ... no
GitLab config exists? ... yes
GitLab config outdated? ... no
Log directory writable? ... yes
Tmp directory writable? ... yes
Uploads directory setup correctly? ... skipped (no tmp uploads folder yet)
Init script exists? ... yes
Init script up-to-date? ... yes
projects have namespace: ... can't check, you have no projects
Redis version >= 2.8.0? ... yes
Ruby version >= 2.1.0 ? ... yes (2.1.2)
Your git bin path is "/usr/bin/git"
Git version >= 2.7.3 ? ... yes (2.8.3)
Active users: 1

Checking GitLab ... Finished
</pre>
