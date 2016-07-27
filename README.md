# MITOC Ansible

This is a collection of Ansible playbooks used in the deployment of the MITOC
Trips web site. The project allows local development on a setup closely mirroring 
that of production as well as a streamlined way to deploy changes.

## Environments
There are playbooks supporting three separate environments:

### Local development (`vagrant.yml`)
For local development, all services run within a virtual machine. Vagrant is
used to automate the creation and modification of this virtual machine. To
create a new virtual machine and provision it with Ansible, simply run:

```bash
vagrant up
```

After that, you will have a fully functional web server accessible at 192.168.33.15

### Development (`development.yml`)
This playbook allows running all infrastructure an AWS. By default, it runs a
Postgres server on the same instance as the webserver, but could easily be
configured to use RDS instead.

Just like a local development machine, the EC2 instance can easily be created
using Vagrant.

1. Store [AWS credentials][aws-credentials] in `.aws/credentials` or directly in the `Vagrantfile`
2. Create a [keypair][aws-keypairs] or import an existing one
3. Launch a new EC2 instance:

    ```bash
    vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box
    vagrant up --provider=aws
    ```

### Production (`production.yml`)
The production playbook contains various secrets used in production, including (but 
not limited to):

- The Django `SECRET_KEY`
- Full SSL certificate for mitoc-trips.mit.edu
- Usernames, hostnames, and passwords for various services
    - RabbitMQ
    - Postgres
    - SES

It also uses some more time-intensive plays that wouldn't be necessary in development
(for example - generating a [strong Diffie-Hellman group][weakdh]).

For obvious reasons, this file is encrypted using Ansible vault.

In the public version of this repository, I have used `git filter-branch` to
completely remove this sensitive file:

    git filter-branch --force --index-filter 'git rm --cached --ignore-unmatch env_vars/production.yml' --tag-name-filter cat -- --all


## History
The repository was modified from Johnathan Calazan's [ansible-django-stack][ansible-django-stack].

## Included services
- Nginx
- Gunicorn
- PostgreSQL
- Supervisor
- Virtualenv
- Celery
- RabbitMQ

[aws-credentials]: http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html#config-settings-and-precedence
[aws-keypairs]: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html
[ansible-django-stack]: https://github.com/jcalazan/ansible-django-stack
[weakdh]: https://weakdh.org/sysadmin.html
