ansible-role-supervisor
=========

Installs and manages supervisord programs and groups.

Requirements
------------

- [python](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)

Role Variables
--------------

Main configuration file for supervisod: `supervisor_conf_file: /etc/supervisord.conf`

Include path for supplementary configuration: `supervisor_include_path: /etc/supervisor`

User that runs supervisord: `supervisor_user: root`

List of all supervisor programs: `supervisor_programs: []`

List of all supervisor groups: `supervisor_program_groups: []`

Enable syslog handling of supervisor logs: `supervisor_use_syslog: false`

Logging directory: `supervisor_log_dir: /var/log/supervisor`

supervisord log file: `supervisor_log_file: "{{supervisor_log_dir}}/supervisord.log"`

User that owns log files. This changes to syslog when `supervisor_use_syslog` is enabled:

    supervisor_log_user: "{{supervisor_user if not supervisor_use_syslog else 'syslog'}}"

Dictionary of supervisord configuration options:

    supervisor_supervisord_conf:
      # logging config is based on whether syslog is in use or not
      logfile:          "{{ supervisor_log_file if not supervisor_use_syslog else 'syslog' }}"
      logfile_maxbytes: "{{ '50MB'              if not supervisor_use_syslog else '' }}"
      logfile_backups:  "{{ '10'                if not supervisor_use_syslog else '' }}"
      loglevel:         "{{ 'info'              if not supervisor_use_syslog else '' }}"
      user:             "{{supervisor_user}}"
      pidfile:          /tmp/supervisord.pid
      nodaemon:         'false'
      minfds:           1024
      minprocs:         200

Default logrotate configuration:

    supervisor_logrotate_conf:
      - daily
      - missingok
      - 'rotate 7'
      - compress
      - delaycompress
      - notifempty
      - "create 660 {{supervisor_log_user}} {{supervisor_user if not supervisor_use_syslog else 'adm'}}"

Dependencies
------------

None

Example Playbooks
----------------

Default configuration:

    - hosts: all
      roles:
         - { role: supervisor, tags: supervisor }

Use syslog for logging:

    - hosts: all
      vars:
        supervisor_use_syslog: true
      roles:
         - { role: supervisor, tags: supervisor }

License
-------

BSD 3-Clause

Author Information
------------------

[SinglePlatform](https://github.com/singleplatform-eng)
