# LDAP-Server-Ansible

Deploy a server using ansible to host an LDAP directory (with self-signed SSL cert) to handle authentication requests from external suppliers.

## Requirements

* `ansible` (1.9.0.1 or later)

* Python things (you may wish to use [virtualenv](https://virtualenv.pypa.io/en/latest/)):
```
pip install -Ur requirements.txt
```

## Fetching Ansible Galaxy playbook dependencies

Use the [ansible-galaxy](http://docs.ansible.com/galaxy.html#advanced-control-over-role-requirements-files) command to install third-party playbooks:

`ansible-galaxy install -r requirements.yml`

## Preparation

For deployment on aws, you must have the following environment variables set:

* [AWS_ACCESS_KEY_ID](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-environment)
* [AWS_SECRET_ACCESS_KEY](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-environment)


## Deployment

`make <PROVIDER_NAME>`


