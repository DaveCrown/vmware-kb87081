# vmware-kb82374 Workaround for CVE-2021-21972 and CVE-2021-21973
## Description
Sets the vRops HTML5 vCenter plugin to incomptatable as a work around per the KB. If you want the knowledge base articles, see the [Reference Section](#Reference) 
### The Play's workflow 
1. SSH's in to the VC and sets the vrops plugin to incompatible. 
2. Restarts the vsphere-ui service if a change was made
3. Make your life easier and you look a rock star.  
 
> ### ***<span style="color:red">Warning!</span>***
>- Mucking config files can break your environment. Have backups and snapshots before begin.
>- You're running code downloaded from the Internet, read the code first to get that warm and fuzzy feeling. 
>- I'm suggesting you generate and copy ssh keys to your vc. Understand the risks and mitigation before you do this.
>- Beyond this point, there be dragons. Proceed at your own risk.
>#### Notes
> This play only works with the VCSA appliance. Windows based vCenters are not supported.  

## Requirements
- **Backups and Snapshots**
- A supported UNIX type OS (Linux, MacOS, etc)
- Ansible (developed against 2.9)
- pyVmomi python library 6.7 or later
- vCenter 6.5 VCSA or later
- git
- a text editor of your choice
- the `root` os passwords for all the vcsa's you want to use this with
### To use the Vagrant file
- Vagrant
- One of following Virtualization Technologies:
    - Virtual Box
    - libvirt
    - Hyper-v
    - VMware Workstation
    - VMware Fusion

## Installation
### git clone
Start with cloning this to your local workstation with `git clone https://github.com/DaveCrown/vmware-kb82374.git` and `cd vmware-kb82374`

### Unix
Use your favorite package manager. See the [Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/index.html). You will also need the pyVmomi Python library. For some reason, the package is not a dependency for Ansible.

### Windows
Either spin up a vm, or use the attached vagrant file to spin a Centos 7 environment. The vagrant file will call the included `install.yml` play to configure the environment with Ansible, Git, and a few other goodies. To install Vagrant, see the [Vagrant Install Guide](https://www.vagrantup.com/intro/getting-started/install.html). You'll also need one of the aforementioned hypervisors.

### Vagrant
The included Vagrant file will spin up a Centos 7 VM, and use the `install.yml` play to install all the required software, copy all the file in this repository over to the `/vagrant` directory. Once Vagrant and a hypervisor has been installed, run `vagrant up`. Once the vmn is built, run `vagrant ssh` to log into the vm. Once in, `cd /vagrant` to get to the files. When your done, `vagrant destory` to stop and remove the vm. You can always rebuild it with `vagrant up` again

## Configuration
You need to define your vcenter environment(s) in the `vcenters.ini` file. It consists of groups and vcenters. How to lay it out is up to you.

### File Format
```ini
[dev]
<appliance fdqn> 
<next appliance fdqn>
...
[prod]
...
```

#### Sample `vcenters.ini` file
```ini
[dev]  
dev_vc.corp.net
[test]  
test_psc.corp.net
test_vc.corp.net
[prod]  
prod_vc_east.corp.net
prod_vc_west.corp.net
prod_psc_east.corp.net
prod_psc_west.corp.net
```
## Usage
### Before you begin
Please make sure your appliances are ansible ready first.
>#### Prerequisites  
>- ssh enabled on all vcenter appliances
>- bash set as default shell on all vcenter appliances, with `chsh -s /bin/bash`. See [vmware KB 2107727]((https://kb.vmware.com/s/article/2107727)). Steps 1 through 5 need to be completed. I like to leave `/bin/bash` as my shell.  
>> #### Required
>- ***Backups!***
>- ssh enabled on all vcenter appliances
>- this git repo cloned to your workstation or as a project in tower.
>- bash set as default shell on all vcenter appliances, with `chsh -s /bin/bash`. See [vmware KB 2107727]((https://kb.vmware.com/s/article/2107727))  
>- `vcenters.ini` file properly configured  
>>#### Optional, but nice  
>- If you don't have an ssh keypair, create a set with `ssh-keygen`. Please Understand the risks first.
>- Copy your ssh keys, if you have them, with `ssh-copy-id root@<your fdqn> -o PreferredAuthentications=password -o PubkeyAuthentication=no`

### Running the Play

#### No sshkeys
Just a simple `ansible-playbook -k apply_kb.yml` is all you need. The flag `-k` will instruct Ansible to prompt for the password.
#### With ssh keys
Call `ansible-playbook apply_kb.yml` without the `-k`.

### Validating the change
Since this is barest of bones play. Please validate the play with the steps outlined [vmware kb 82374](https://kb.vmware.com/s/article/82374).

### Options
#### CLI 
| option | usage |
| --- | --- |
| `-k` | prompt for ssh password, not needed if you have ssh keys setup |
| `-e` | Use variables, see below |  
#### Variables
| option| usage |
| --- | --- |
| `env=<blah>` | Optionally Target only vcenter environment , as defined in `vcenters.ini` |

### Ansible Tower
To use the play in Tower, this play just needs a standard machine credential. The vcenters.ini file is your inventory file for the project.

## Reference
[vmware kb 82374](https://kb.vmware.com/s/article/82374)  
[vmware kb 76719](https://kb.vmware.com/s/article/76719)  
[vmware kb 2107727](https://kb.vmware.com/s/article/2107727)  
[Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/index.html)  
[Vagrant Install Guide](https://www.vagrantup.com/intro/getting-started/install.html)  

## Legal
I am in no away affiliated with VMware, nor did I write the fix. I just wrote an ansible play to apply it at scale. Use this as your own peril with good backups and snapshots. Don't blame me if this burns down your vcenter environment, you were warned. I take no responsibility or liability.

Trademarks and Copyrights are properties of their respective owners.