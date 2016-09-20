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

<pre>

  roles:
    - {
        role: "sa-gitlab-ce",

        option_install_nodejs: true,
        option_install_postgres: true,
        option_install_ruby: true,
        option_ruby_install_setsystem: true,
        option_install_nginx: true,
        option_install_redis: true,
        option_install_redis_commander: false,
        option_install_go: true,
        option_install_git: true,

        gitlab_version: "v8.9.1",
        gitlab_external_url: http://gitlab.lvh.me,
        gitlab_host: git.lvh.me,
        gitlab_port: 443,
        gitlab_https: true,

        gitlab_db_key_base: N1seGKyllvLIO2v9LA2B,

        # postgres
        db_host: localhost,
        db_user: git,
        db_password: app_password,
        db_name: gitlabhq_production
      }

</pre>

Install GitLab EE edition:

<pre>

  roles:
    - {
        role: "sa-gitlab-ce",

        option_install_nodejs: true,
        option_install_postgres: true,
        option_install_ruby: true,
        option_ruby_install_setsystem: true,
        option_install_nginx: true,
        option_install_redis: true,
        option_install_redis_commander: false,
        option_install_go: true,
        option_install_git: true,

        gitlab_version: "v8.9.1-ee",
        gitlab_repo_url: "https://gitlab.com/gitlab-org/gitlab-ee.git",
        gitlab_external_url: http://gitlab.lvh.me,
        gitlab_host: git.lvh.me,
        gitlab_port: 443,
        gitlab_https: true,

        gitlab_db_key_base: N1seGKyllvLIO2v9LA2B,

        # postgres
        db_host: localhost,
        db_user: git,
        db_password: app_password,
        db_name: gitlabhq_production
      }

</pre>



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


# HARD MANUAL PART


configuring grean seal https certificates with sa-letsencrypt
-------------------------------------------------------------

In case of issues with retrieving certs, we need to ensure, that
provisioning created default site like below, where directory acme-challenge
is properly mapped, and server_name contains list of domains we are getting
certificates for.

```

  server {
      listen 80 default_server;
      server_name domain.com www.domain.com;
      location /.well-known/acme-challenge/ { alias /opt/letsencrypt/.acme-challenges/; }

      location / {
           default_type text/plain;
           return 200 "scheme: $scheme";
      }
  }
```


manual adjustments to make all parts running
--------------------------------------------

UNFORTUNATELY, there is still bunch of manual steps that need to be adjusted to get things finally up

Lets encrypt credentials adjustments
++++++++++++++++++++++++++++++++++++

Once letsencrypt retrieved certificates, they are located under
`/opt/letsencrypt/certs/domain.com

```

  drwxr-x--- 2 le git 4096 Aug 16 13:29 .
  drwxr-x--- 3 le git 4096 Jul 20 10:25 ..
  -rw-r----- 1 le git 1720 Jul 20 10:25 cert-1469010338.csr
  -rw-r----- 1 le git 2208 Jul 20 10:25 cert-1469010338.pem
  -rw-r----- 1 le git 1756 Aug 16 13:26 cert-1471353980.csr
  -rw-r----- 1 le git    0 Aug 16 13:26 cert-1471353980.pem
  -rw-r----- 1 le git 1756 Aug 16 13:29 cert-1471354167.csr
  -rw-r----- 1 le git 2244 Aug 16 13:29 cert-1471354167.pem
  lrwxrwxrwx 1 le git   19 Aug 16 13:29 cert.csr -> cert-1471354167.csr
  lrwxrwxrwx 1 le git   19 Aug 16 13:29 cert.pem -> cert-1471354167.pem
  -rw-r----- 1 le git 1647 Jul 20 10:25 chain-1469010338.pem
  -rw-r----- 1 le git 1647 Aug 16 13:29 chain-1471354167.pem
  lrwxrwxrwx 1 le git   20 Aug 16 13:29 chain.pem -> chain-1471354167.pem
  -rw-r----- 1 le git 3855 Jul 20 10:25 fullchain-1469010338.pem
  -rw-r----- 1 le git 3891 Aug 16 13:29 fullchain-1471354167.pem
  lrwxrwxrwx 1 le git   24 Aug 16 13:29 fullchain.pem -> fullchain-1471354167.pem
  -rw-r----- 1 le git 3247 Jul 20 10:25 privkey-1469010338.pem
  -rw-r----- 1 le git 3243 Aug 16 13:26 privkey-1471353980.pem
  -rw-r----- 1 le git 3243 Aug 16 13:29 privkey-1471354167.pem
  lrwxrwxrwx 1 le git   22 Aug 16 13:29 privkey.pem -> privkey-1471354167.pem
```

Problem would be that by default after renew, certificates can be read by le (letsencrypt) user only.
We need to allow group gitlab (git) to read all the certificates too.

command
`sudo chown -R le:git /opt/letsencrypt/certs/domain.com`

command
`sudo chmod -R g+r /opt/letsencrypt/certs/domain.com`


Running docker registry combined with gitlab signon
++++++++++++++++++++++++++++++++++++++++++++++++++++

To run our docker registry we make use of docker-compose tool.

Our configuration file docker-compose.yml for docker registry is:

```

  registry:
    restart: always
    image: registry:2
    container_name: registry
    ports:
      - 5000:5000
    environment:
      REGISTRY_AUTH_TOKEN_REALM: https://domain.com/jwt/auth
      REGISTRY_AUTH_TOKEN_SERVICE: container_registry
      REGISTRY_AUTH_TOKEN_ISSUER: gitlab-issuer
      REGISTRY_AUTH_TOKEN_ROOTCERTBUNDLE: /certs/domain.com/fullchain.pem
      SOME_WSI_VAR: some_domain_var
    volumes:
      - /var/lib/registry:/var/lib/registry #/data
      - /opt/letsencrypt/certs:/certs
```

Directory /var/lib/registry needs to be created in advance.
I usually create it as
`drwxrwxr-x  ubuntu   docker   registry`
but once we restore our gitlab backup it will change to
`drwxr-x---  git      git    registry`

to run registry initially, first we do `docker-compose create` and later
`docker-compose run`

Service is restarted automatically after box reboot.

Adjusting nginx configuration for gitlab
++++++++++++++++++++++++++++++++++++++++

You will already have site configured via provisioning step in
`/etc/nginx/sites-enabled/gitlab`

You would need to modify that file in a following way:

- Fully remove http listener for gitlab
- In https listener specify path to certificates as described below

```

  server {
    listen 0.0.0.0:443 ssl;
    listen [::]:443 ipv6only=on ssl default_server;
    server_name domain.com; ## Replace this with something like gitlab.example.com
    server_tokens off; ## Don't show the nginx version number, a security best practice

    ## Strong SSL Security
    ## https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html & https://cipherli.st/
    ssl on;
    ssl_certificate /opt/letsencrypt/certs/domain.com/fullchain.pem;
    ssl_certificate_key /opt/letsencrypt/certs/domain.com/privkey.pem;
    # GitLab needs backwards compatible ciphers to retain compatibility with Java IDEs
    ssl_ciphers "ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 5m;
```

Adjusting nginx configuration for docker registry
+++++++++++++++++++++++++++++++++++++++++++++++++

Ensure, that for our docker registry, nginx serves registry.domain.com


```

  # Docker registry proxy for api versions 1 and 2

  upstream docker-registry {
    server localhost:5000;
  }

  upstream docker-registry-v2 {
    server localhost:5000;
  }

  # No client auth or TLS
  server {

    listen 443 ssl;
    server_name registry.domain.com;

    ssl_certificate /opt/letsencrypt/certs/domain.com/fullchain.pem;
    ssl_certificate_key /opt/letsencrypt/certs/domain.com/privkey.pem;

    #ssl config per https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    ssl_ciphers "EECDH+ECDSA+AESGCM:EECDH+aRSA+AESGCM:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA256:EECDH+ECDSA+SHA384:EECDH+ECDSA+SHA256:EECDH+aRSA+SHA384:EDH+aRSA+AESGCM:EDH+aRSA+SHA256:EDH+aRSA:EECDH:!aNULL:!eNULL:!MEDIUM:!LOW:!3DES:!MD5:!EXP:!PSK:!SRP:!DSS:!RC4:!SEED";
    ssl_prefer_server_ciphers on;

    ssl_dhparam /etc/nginx/dhparam.pem;

    #only supported since 1.3.7
    ssl_stapling on;
    ssl_stapling_verify on;

    # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
    # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
    # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
    # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
    ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
    ssl_session_timeout  10m;

    add_header Strict-Transport-Security max-age=63072000;
    add_header X-Frame-Options DENY;
    add_header X-Content-Type-Options nosniff;

    # disable any limits to avoid HTTP 413 for large image uploads
    client_max_body_size 0;

    # required to avoid HTTP 411: see Issue #1486 (https://github.com/docker/docker/issues/1486)
    chunked_transfer_encoding on;

    location /v2/ {
      # Do not allow connections from docker 1.5 and earlier
      # docker pre-1.6.0 did not properly set the user agent on ping, catch "Go *" user agents
      if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
        return 404;
      }

      # To add basic authentication to v2 use auth_basic setting plus add_header
      # auth_basic "docker registry";
      # auth_basic_user_file test.password;
      # add_header 'Docker-Distribution-Api-Version' 'registry/2.0' always;

      proxy_pass                          http://docker-registry-v2;
      proxy_set_header  Host              $http_host;   # required for docker client's sake
      proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
      proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header  X-Forwarded-Proto $scheme;
      proxy_read_timeout                  900;
    }

    location / {
      # v1 - docker-registry.conf
      proxy_pass                          http://docker-registry;
      proxy_set_header  Host              $http_host;   # required for docker client's sake
      proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
      proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header  X-Forwarded-Proto $scheme;
      proxy_set_header  Authorization     ""; # For basic auth through nginx in v1 to work, please comment this line
      proxy_read_timeout                  900;
    }
  }
```

Hugh set of manual corrections to the gitlab config itself
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

We navigate to   /home/git/gitlab/config/gitlab.yml  and start patching :(

Host settings
~~~~~~~~~~~~~

Change host and port to match nginx setup

```

  < # GitLab application config file #
  ---
  > # GitLab application config file  #
  32,34c32,34
  <     host: domain.com
  <     port: 443 # Set to 443 if using HTTPS, see installation.md#using-https for additional HTTPS configuration details
  <     https: true # Set to true if using HTTPS, see installation.md#using-https for additional HTTPS configuration details
  ---
  >     host: localhost
  >     port: 80 # Set to 443 if using HTTPS, see installation.md#using-https for additional HTTPS configuration details
  >     https: false # Set to true if using HTTPS, see installation.md#using-https for additional HTTPS configuration details
```

Adjust email_from email_to settings

```

  70c70
  <     email_from: no-reply@domain.com
  ---
  >     email_from: example@example.com
  72c72
  <     email_reply_to: no-reply@domain.com
  ---
  >     email_reply_to: noreply@example.com
```  

Docker registry settings
~~~~~~~~~~~~~~~~~~~~~~~~

Uncomment and adjust part responsible for running docker registry

```

  <      enabled: true
  <      host: registry.domain.com
  ---
  >     # enabled: true
  >     # host: registry.example.com
  183,186c227,230
  <      api_url: http://localhost:5000/ # internal address to the registry, will be used by GitLab to directly communicate with API
  <      key: /opt/letsencrypt/certs/domain.com/privkey.pem  # [VS] Changed from key_path
  <      path: /var/lib/registry
  <      issuer: gitlab-issuer
  ---
  >     # api_url: http://localhost:5000/ # internal address to the registry, will be used by GitLab to directly communicate with API
  >     # key: config/registry.key
  >     # path: shared/registry
  >     # issuer: gitlab-issuer
```  

Signin using external providers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Uncomment and configure external providers

```

  312c415
  <     enabled: true
  ---
  >     enabled: false
  340c443
  <     external_providers: ["github", "gitlab"]
  ---
  >     external_providers: []
  359,364c462,467
  <        - { name: 'github',
  <            app_id: 'CENSORED',
  <            app_secret: 'CENSORED',
  <            url: "https://github.com/",
  <            verify_ssl: true,
  <            args: { scope: 'user:email' } }
  ---
  >       # - { name: 'github',
  >       #     app_id: 'YOUR_APP_ID',
  >       #     app_secret: 'YOUR_APP_SECRET',
  >       #     url: "https://github.com/",
  >       #     verify_ssl: true,
  >       #     args: { scope: 'user:email' } }
  368,371c471,474
  <        - { name: 'gitlab',
  <            app_id: 'CENSORED',
  <            app_secret: 'CENSORED',
  <            args: { scope: 'api' } }
  ---
  >       # - { name: 'gitlab',
  >       #     app_id: 'YOUR_APP_ID',
  >       #     app_secret: 'YOUR_APP_SECRET',
  >       #     args: { scope: 'api' } }
```  

Database connection
~~~~~~~~~~~~~~~~~~~

ENSURE, that provisioner created the right database.yml file in
the config directory

```

  #
  # PRODUCTION
  #
  production:
    adapter: postgresql
    encoding: unicode
    database: gitlabhq_production
    pool: 10
    username: git
    password: CENSORED
    host: localhost
    port: 5432
```    

Secrets and key bases
~~~~~~~~~~~~~~~~~~~~~

If you plan to restore gitlab from backup, ENSURE that secrets.yml
contains the same salts in BASE parameters as on original box.


SMTP settings
~~~~~~~~~~~~~

Ensure, that in config/initializers there is a file called smtp_settings.rb
Compared to sample, it should contain mail credentials.
For AWS and amazon SES required changes look   like


```

  15,19c15,19
  <     address: "email-smtp.us-east-1.amazonaws.com",
  <     port: 587,
  <     user_name: "CENSORED",
  <     password: "CENSORED",
  <     domain: "domain.com",
  ---
  >     address: "email.server.com",
  >     port: 465,
  >     user_name: "smtp",
  >     password: "123456",
  >     domain: "gitlab.company.com",
  21,22c21,22
  <     enable_starttls_auto: true#,
  <  #   openssl_verify_mode: 'peer' # See ActionMailer documentation for other possible options
  ---
  >     enable_starttls_auto: true,
  >     openssl_verify_mode: 'peer' # See ActionMailer documentation for other possible options
```

Step 5 - fortunately at this step we should be up
-------------------------------------------------

At a moment of the writing article it was enough to put our dedicated
gitlab host up with all the services and be able to unpack gitlab backup
from githost.io

