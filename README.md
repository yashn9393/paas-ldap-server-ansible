# LDAP-Server-Ansible

Deploy a server using ansible to host an LDAP directory (with self-signed SSL cert) to handle authentication requests from external suppliers.

## Requirements

* `ansible` (1.9.0.1 or later)

* Python things (you may wish to use [virtualenv](https://virtualenv.pypa.io/en/latest/)):
```
pip install -Ur requirements.txt
```

* AWS EIP for the server. You need to allocate this by hand and update the vault with the EIP you get.

* For provisioning on AWS you will need to have your AWS access credentials exported as environment variables for ansible to pick up.
```
export AWS_SECRET_ACCESS_KEY=<your secret access key>
export AWS_ACCESS_KEY_ID=<your access key id>
```

* [GnuPG](#setting-up-gpg-encrypted-vault-password-support)

#### Setting up GPG-encrypted vault-password support

You will need to have setup [gpg-agent](https://www.gnupg.org/) on your computer before you start.

##### Apple specific

Install the latest [GPG Tools Suite for MacOX](https://gpgtools.org/)

```
brew install pwgen
brew install gpg
brew install gpg-agent
```

##### Ubuntu specific

Install the [GNU Privacy Guard encryption suite](https://www.gnupg.org/):

```
sudo apt-get update
sudo apt-get install pwgen
sudo apt-get install gnupg2
sudo apt-get install gnupg-agent
sudo apt-get install pinentry-curses
```

##### Common

If you haven't already generated your pgp key (it's ok to accept the default options if you never done this before):

```
gpg --gen-key
```

Get your KEYID from your keyring:

```
gpg --list-secret-keys | grep sec
```

This will probably be pre-fixed with 2048R/ and look something like 93B1CD02

Send your public key to pgp key server :

```
gpg --keyserver pgp.mit.edu --send-keys KEYID
```


Create ~/.bash_gpg:

```
envfile="${HOME}/.gnupg/gpg-agent.env"

if test -f "$envfile" && kill -0 $(grep GPG_AGENT_INFO "$envfile" | cut -d: -f 2) 2>/dev/null; then
    eval "$(cat "$envfile")"
else
    eval "$(gpg-agent --daemon --log-file=~/.gpg/gpg.log --write-env-file "$envfile")"
fi
export GPG_AGENT_INFO  # the env file does not contain the export statement
```

Add to ~/.bashrc

```
GPG_AGENT=$(which gpg-agent)
GPG_TTY=`tty`
export GPG_TTY

if [ -f ${GPG_AGENT} ]; then
    . ~/.bash_gpg
fi
```

##### Ubuntu specific

Create ~/.gnupg/gpg-agent.conf

```
default-cache-ttl 600
pinentry-program /usr/bin/pinentry
max-cache-ttl 172800
```

##### Final step

Start a new shell or source your bashrc i.e. `. ~/.bashrc`




## Fetching Ansible Galaxy playbook dependencies

Use the [ansible-galaxy](http://docs.ansible.com/galaxy.html#advanced-control-over-role-requirements-files) command to install third-party playbooks:

`ansible-galaxy install -r requirements.yml`

## Preparation

For deployment on aws, you must have the following environment variables set:

* [AWS_ACCESS_KEY_ID](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-environment)
* [AWS_SECRET_ACCESS_KEY](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#cli-environment)


## Deployment

`make aws`
`make site` in case your server exists already.

## Variables
### Globals defaults
```
ami_id: "ami-47a23a30"               # Ubuntu 14.04
ssh_key_name: <your_key_name>        # AWS SSH key name to be used for the server
```

### Vault contents
```
---
r53_zone: "<your_r53_dns_zone_name>"   # Use zone dns name here, not the ID
dns_name: "<your_dns_server_name>"     # This is the server name in the domain
				       # The above combine to create {{ dns_name }}.{{ r53_zone }}. DNS record for your server
public_eip: "<your_EIP>"
ldap_root_password: "changeme"
users:
  - login: "john_doe"
    name: "John Doe"
    password: "johnspassowrd"
    uid: "10001"
    gid: "5001"
  - login: "jane_doe"
    name: "Jane Doe"
    password: "janespassword"
    uid: "10002"
    gid: "5002"

```
