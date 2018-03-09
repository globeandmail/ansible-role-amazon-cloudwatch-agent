ansible-role-amazon-cloudwatch-agent
======

This role will install the AWS CloudWatch Agent on Ubuntu (64-bit, precise/trusty/xenial).
- config and credentials are stored in /root/.aws/
- the process runs as root
- in the configs, `amazon-cloudwatch-agent.json` effectively does nothing.  The .toml config was generated from the .json, but in our case the .toml contains some extra tweaks that cannot be added in the .json -- so, the purpose of the .json file is to stand as a reference for the .toml, and should be kept up-to-date in case a new .toml needs to be generated (and then tweaked)


Variables
------

```
aws_cwa_region      -  (Optional) AWS region.  Defaults to us-east-1.
aws_cwa_namespace   -  The namespace in which your metrics will reside (e.g. 'myApplication')
aws_cwa_key_access  -  AWS access key
aws_cwa_key_secret  -  AWS secret key
aws_cwa_cfgs        -  Path (absolute, or relative to playbook) containing the .j2 templates for the CloudWatch Agent configs
                       NOTE: These must be templates, as they necessarily contain {{ansible_hostname}}
                             Only files with the .j2 extension will be processed; the '.j2' will be removed
```

It's recommended that you declare `aws_cwa_key_access` and `aws_cwa_key_secret` in either a vault-encypted var file, or as encrypted strings within a var file.  If you want, you can provide them as plaintext extra_vars when running the playbook.



Example Playbook
------

```
---
- hosts: all
  become: yes
  become_user: root
  vars:
    aws_cwa_namespace: 'your-metric-namespace'
    aws_cwa_cfgs: 'group_files/awscwa_cfg/webserver-stg/'
  roles:
  - ansible-role-amazon-cloudwatch-agent

```

Example Dir Structure

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
            └── main.yml
```


Author Information
------

abarrett@globeandmail.com
