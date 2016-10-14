
ansible-role-awscli
=========

Installs and manages supervisord programs and groups.

https://galaxy.ansible.com/singleplatform-eng/supervisor/

Role Variables
--------------

- `supervisor_conf_file`: where to store the main config file (Default: /etc/supervisord.conf)

- `supervisor_include_path`: where to store program and group config files (Default: /etc/supervisor)

- `supervisor_user`: user that runs supervisord (Default: root)

- `supervisor_use_syslog`: enable syslog for supervisord (Default: false)

- `supervisor_log_user`: user that owns log files (Default: "{{supervisor_user if not supervisor_use_syslog else 'syslog'}}")

- `supervisor_log_dir`: where to store log files (Default: /var/log/supervisor)

- `supervisor_log_file`: where to store supervisord log file (Default: "{{supervisor_log_dir}}/supervisord.log")

- `supervisor_programs`: list of supervisor programs (Default: [])

        # Example
        supervisor_programs:
          - name: app
            command: start_app
            environment:
              PATH: /srv/app/bin
            directory: /srv/app
            user: app_user
            autostart: true
            autorestart: true
            stopasgroup: true

- `supervisor_program_groups`: list of supervisor groups (Default: [])

        # Example
        supervisor_program_groups:
          - name: app_group
            programs: app1,app2

- `supervisor_supervisord_conf`: supervisord configuration

        # Default
        supervisor_supervisord_conf:
          # logging config is based on whether syslog is in use or not
          logfile:          "{{ supervisor_log_file if not supervisor_use_syslog else 'syslog' }}"
          logfile_maxbytes: "{{ '50MB'              if not supervisor_use_syslog else '' }}"
          logfile_backups:  "{{ '10'                if not supervisor_use_syslog else '' }}"
          loglevel:         "{{ 'info'              if not supervisor_use_syslog else '' }}"
          pidfile:          /tmp/supervisord.pid
          nodaemon:         'false'
          minfds:           1024
          minprocs:         200

- `supervisor_logrotate_conf`: supervisor logrotate configuration

        # Default
        supervisor_logrotate_conf:
          - daily
          - missingok
          - 'rotate 7'
          - compress
          - delaycompress
          - notifempty
          - "create 660 {{supervisor_log_user}} {{supervisor_user if not supervisor_use_syslog else 'adm'}}"

Example Playbook
----------------

    ---
    - hosts: localhost
      become: true
      vars:
        supervisor_programs:
          - name: app
            command: start_app
      roles:
        - supervisor
  

Dependencies
------------------

- [python](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)

[ansible-role-python](https://github.com/singleplatform-eng/ansible-role-python) can satisfy these dependencies.

Author Information
------------------

[SinglePlatform Engineering](http://engineering.singleplatform.com/)

License
-------

BSD 3-Clause
