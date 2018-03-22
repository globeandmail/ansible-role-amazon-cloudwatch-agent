ansible-role-amazon-cloudwatch-agent
======

This role will install the AWS CloudWatch Agent on Ubuntu (64-bit, precise/trusty/xenial).
- config and credentials are stored in /root/.aws/
- the process runs as root
- in the configs, `amazon-cloudwatch-agent.json` effectively does nothing.  The .toml config was generated from the .json, but in our case the .toml contains some extra tweaks that cannot be added in the .json -- so, the purpose of the .json file is to stand as a reference for the .toml, and should be kept up-to-date in case a new .toml needs to be generated (and then tweaked)

*See the subdir "ExampleCfgs" for an example of how to configure this role.*

Variables
------

```
aws_cwa_region             - (Optional) AWS region.  Defaults to "us-east-1"
aws_cwa_namespace          - The namespace in which your metrics will reside (e.g. 'myApplication')
aws_cwa_logfiles           - (Optional) List of logfiles that you wish to monitor, and their associated parameters -- for example:
  - file_path: '/var/log/syslog'
    log_group_name: 'log_group_name_goes_here'
    log_group_tags:
      - TagKey: TagValue
    log_stream_name: '{{ansible_hostname}}/log_stream_name'
    log_retention: '7'
    timestamp_format: '%b %-d %H:%M:%S'
    timestamp_layout: 'Jan 2 15:04:05'
    timestamp_regex: '^(\\w{3}\\s+\\d{1,2} \\d{2}:\\d{2}:\\d{2}).*$'
    timezone: 'LOCAL'
aws_cwa_disk_monitor_paths - (Optional) List of paths (i.e. different partitions) that you wish to monitor for space.  Defaults to "/".  For example:
  - "/"
  - "/opt"
aws_cwa_key_access         - AWS access key
aws_cwa_key_secret         - AWS secret key
aws_cwa_cfgs               - Path (absolute, or relative to playbook) containing the .j2 templates for the CloudWatch Agent configs
                                NOTE: These must be templates, as they necessarily contain {{ansible_hostname}}
                                Only files with the .j2 extension will be processed.  The '.j2' extension will be removed.
```

It's recommended that you declare `aws_cwa_key_access` and `aws_cwa_key_secret` in either a vault-encypted var file, or as vault-encrypted strings within a var file.



Example Playbook
------

```
---
- hosts: all
  become: yes
  become_user: root
  roles:
  - ansible-role-amazon-cloudwatch-agent

```

Example Dir Structure
------

```
.
├── group_files
│   └── awscwa_cfg
│       └── webserver-stg
│           ├── amazon-cloudwatch-agent.json.j2
│           └── amazon-cloudwatch-agent.toml.j2
├── group_vars
│   └── all.yml
├── inventory
├── main.yml
└── roles
    └── ansible-role-amazon-cloudwatch-agent
        ├── files
        │   └── start-amazon-cloudwatch-agent
        ├── handlers
        │   └── main.yml
        ├── README.md
        └── tasks
            ├── main.yml
            ├── ubuntu-12.yml
            ├── ubuntu-14.yml
            └── ubuntu-16.yml
```

Author Information
------

abarrett@globeandmail.com
