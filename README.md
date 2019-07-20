# Google Cloud Ansible Example

Example project for configuring the following:

* Create and tear down Compute Engine VM Instances using Ansible
  * Includes VPC Network and associated subnets and firewall rules

# Pre-requisites

## Setting up Google Cloud IAM

[Creating and enabling service accounts for instances](https://cloud.google.com/compute/docs/access/create-enable-service-accounts-for-instances#createanewserviceaccount)

### Configuring GCP ENV Variables
After Google Cloud IAM is configured, configure ENV variables for GCP, for example.

```
export GCP_PROJECT=gcp-project-name
export GCP_AUTH_KIND=serviceaccount
export GCP_SERVICE_ACCOUNT_FILE=/tmp/user@domain-gcp-1.iam.gserviceaccount.com.json
export GCP_SCOPES=https://www.googleapis.com/auth/compute
export GCP_REGION=australia-southeast1
export GCP_ZONE=australia-southeast1-b
```

## Installing Python 3

Download and install Python3 from https://www.python.org/downloads/mac-osx/.  This is the default Python for the OS.  All Python packages should ideally be managed under venv from this point onwards.

### Install project dependencies using Python venv

#### Create new Python venv
```
$ python -m venv .venv
$ source .venv/bin/activate
```

#### To de-activate current Python venv
```
$ deactivate
```

#### Installing Python dependencies
```
$ pip install -r requirements.txt
```

### Setting up SSH keys for the new instance

Note this is not the same SSH key you used for your GCP service account.  This is the SSH key used for accessing the newly created instance.
```
$ ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519_gcp_compute_gcp-ansible -C "first.last@domain.com"
```

## Create GCP Compute Instance using Ansible

```
$ ansible-playbook -i inventories/gcp-ansible.gcp.yml playbooks/10_infras.yml --tags "create"
```

Login to instance using IP address that can be found in output logs from above commands
```
$ ssh gcp-ansible@35.244.126.18
```

Example console after creation and logging into new instance:
```
➜ ssh gcp-ansible@35.244.126.18
The authenticity of host '35.244.126.18 (35.244.126.18)' can't be established.
ECDSA key fingerprint is SHA256:cqiZHFL9KDTvFpgv3GGkSyk+eARBCWyp75j9ldcBLaA.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '35.244.126.18' (ECDSA) to the list of known hosts.
[gcp-ansible@gcp-ansible ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       100G  1.6G   99G   2% /
devtmpfs        841M     0  841M   0% /dev
tmpfs           848M     0  848M   0% /dev/shm
tmpfs           848M  8.4M  840M   1% /run
tmpfs           848M     0  848M   0% /sys/fs/cgroup
tmpfs           170M     0  170M   0% /run/user/1002
```

To delete the instance
```
$ ansible-playbook -i inventories/gcp-ansible.gcp.yml playbooks/10_infras.yml --tags "delete"
```

Delete line from ~/.ssh/known_hosts with new instance IP address after deleting instance.

## TODO

* Extend current Ansible config scripts to further configure created instance
  * Install and configure JVM stack
* Use Ansible to configure Google Cloud SQL
* Is there an easier way to log the IP address created?

## Docs and References

[Ansible Google Cloud Platform Guide](https://docs.ansible.com/ansible/latest/scenario_guides/guide_gce.html#providing-credentials-as-environment-variables)

[Getting Started with Red Hat Ansible for Google Cloud Platform](https://programmaticponderings.com/tag/google-compute-engine/)
