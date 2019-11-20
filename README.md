# Data Carpentry Genomics Ansible setup

This Ansible playbook allows you to setup an Ubuntu instance (18.04) ready for a [Data Carpentry Genomic Workshop](https://datacarpentry.org/genomics-workshop/). This allows the workshop to easily be run on various cloud platforms.

You'll typically want two cores and ~4Gb RAM per instance.

## Requirements

* [Ansible](https://github.com/ansible/ansible) (eg `pip install ansible`)

## Usage

1. Edit `hosts` to add the IP(s) or hostname(s) of your instances.
   Ensure you've run `ssh-add ~/.ssh/some_key` to allow you to access these without a password.
   The password for the `dcuser` account can be changed in `vars/all.yml`.

2. Run the playbook:
```bash
ansible-playbook -i hosts training-instance.yml
```

Typically you'd run this on a single instance, snapshot it to create an image and then launch many instances for workshop participants based on that image.
