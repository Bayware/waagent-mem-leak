# Reproducing WALinuxAgent Memory Leak

## Create Azure VM

In Azure Portal, create a virtual machine using Ubuntu Server 18.04 LTS image.  I used
West US on Standard B1ms (1 cpu, 2GiB memory).  Add SSH credentials and user name and set up
networking as appropriate.

On the Advanced tab, click *select an extension to install* and then select
**Custom Script for Linux**.  Click create.  On the **Install extension** page,
copy/paste the dummy command from `command-to-execute.txt` in this repo into **Command** field.
Click OK.

Finish creating the VM as typical.

## Set Up Ansible

Replace the IP address in `hosts` file with the IP address of the new VM.  Set up the
`ansible.cfg` with the `remote_user` and `private_key_file` associated with the new
VM.

## Run Ansible

You will a key to access to the repository that has Bayware ib-agent.

```
]$ ansible-playbook -e repo_key_id="<from-Bayware>" -e repo_secret_key="<from-Bayware>" start-leak.yml
```

## Check VM Memory

Log into your VM, switch to root, and run `htop`.  When viewed as a tree, subprocesses
under `/usr/bin/python3 -u /usr/sbin/waagent -daemon` show leak.  (That is, processes
with `WALinuxAgent` in the command name.)  Memory leak is evident within a couple
minutes as usage slowly climbs over 2% (say, at 5% within 30 minutes elapsed time).

## Version Information

```
root@ms-extension5:~# waagent version
WALinuxAgent-2.2.40 running on ubuntu 18.04
Python: 3.6.8
Goal state agent: 2.2.41
root@ms-extension5:~# openssl version
OpenSSL 1.1.1  11 Sep 2018
```


