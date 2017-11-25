localcloud-nginx
=========

This role will install nginx and allow you to easily manage your virtual hosts via yaml config offering extensibility of applying configuration templates.
Comes with simple virtual host template as well as ones for common app deployments, such as Symfony 1& 2 (production/development), Wordpress or generic PHP apps. More templates will be added later and you can always use your own!
Additionally this role can upload SSL/TLS certificates and enable HTTPS support for managed virtual hosts.

Requirements
------------

An Ubuntu 14 LTS box.

Dependencies
------------

This role has no dependencies.

Role Variables
--------------

`nginx_manage_config`
Default: False. Whether to allow Ansible to manage nginx config.

`nginx_manage_vhosts`
Default: False.  Whether to allow Ansible to manage virtual hosts configuration.


`vhost_templates`
Default: 'roles/localcloud-nginx/templates/vhosts'. Path to default virtual hosts templates.

`vhosts`
Default: []. A dictionary of virtual hosts configuration. Will be compiled into nginx configuration. See section below.


Virtual hosts config example
----------------

You are not required to have any virtual hosts configuration, so you can manage your virtual hosts manually. But it's actually very convenient to do so because you end up with a reproducible set of configuration instructions.
To manage virtual hosts make sure the `nginx_manage_vhosts` is on, and then define you virtual hosts configuration in a `vhosts` block.
You can either define it directly in you playbook, or have it as included config file with is more convenient.

For example:
```yml
#  main playbook

- hosts: all
  roles:
    - localcloud-nginx

  pre_tasks:
    - name: include configs
      include_vars: '{{item}}'
      with_items:
        - config/vhosts.yml
      tags: always
```

Your vhosts configuration can then look like this:

```yml
# config/vhosts.yml
---
vhosts:

  # hostname as dict key is a good practice (all configs will be named based on this)
  your.url.com:
    # template to use, either default one, or your own
    template: '{{vhost_templates}}/wordpress.j2'
    listen:
      - '80'

    # your host names
    server_name: 'your.url.com'

    # your index files
    index: 'index.html index.php'
    
    # fast cgi environment variables
    fast_cgi_env_vars:
        APP_ENV: 'production'
        APP_CACHE: 'on'        

    # upload ssl certificates, and enable https for this host, both crt and key required
    ssl:
        crt: 'ssl/certificate.crt' # relative to main playbook
        key: 'ssl/keyfile.key'
        
    # enable CORS from these URLs
    cors:
        origins:
          - 'https://site1.com'
          - 'http://site2.com'
```


Example Playbook
----------------

```yml
- hosts: servers
  roles:
     - localcloud-nginx
  vars:
     box_user: username
```

License
-------

MIT
