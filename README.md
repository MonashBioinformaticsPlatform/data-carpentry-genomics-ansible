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


## Openstack / NeCTAR example

If you would like to launch a set of instance on Openstack / NeCTAR using the shell you'll need the `openstack` 
commandline client (`pip install python-openstackclient`), source your credentials (`source My-Nectar-Project-openrc.sh`) and run something like:

```bash
openstack server create \
  --image data-carpentry-genomics-20191121 \
  --key-name mbp_hosts \
  --flavor m3.small \
  --availability-zone monash-01 \
  --security-group default \
  --max 20 \
  --wait \
  dc-genomics-training
```

This assumes there is a public/community image or snapshot named `data-carpentry-genomics-20191121` - this one exists in NeCTAR since I created it.

You can get a list of the running server IPs to paste into the Etherpad with:
```bash
openstack server list --format csv | tail -n +2 | \
  grep dc-genomics-training | cut -f 4 -d ',' | \
  sed 's/\"//g' | cut -f 2 -d '=' | grep . | sort -h
```

When the workshop is finished and you want to destroy all the servers:
```bash
# Careful - all servers and their data in the current 'project'
# with 'dc-genomics-training' in the name will be destroyed, unrecoverably.
server_id_list=$(openstack server list --format csv | tail -n +2 | grep dc-genomics-training | cut -f 1 -d ',' |   sed 's/\"//g' | cut -f 2 -d '=' | grep . | sort -h | xargs)
openstack server delete ${server_id_list}
```

## TODO

* Add RStudio Server
* Edit .bashrc (remove `ll` alias, maybe other things)
* Automate generating a NeCTAR / OpenStack image after instance setup, eg http://khmel.org/?p=1188
* Create a testing task that runs through the Data Wrangling section commands to test that the instance works.
