#About

With this collection of playbooks, you can set up your own Proxy server running Squid3 in AWS.

Works good enough on a t2.micro instance, free-tier eligible.

In case you already have the instance ready, or you're not on AWS, you can simply use the squid.yml playbook as shown below.

I chose not to use dynamic inventory because the major benefits of using it are not applicable in this case.

The playbook has a task that creates the SSH Key Pair in your .ssh folder and uploads it to AWS. Of course, you need that key if you want to SSH into the instance.

Much is left to be desired, but then again, this whole thing is more of a proof of concept. If I'll get the time one day, I might dump the waterfall-like inclusions and go for something smarter.

#Tools used

```
ansible --version                                                                      
ansible 1.9 (devel 21a5a5f50f) last updated 2015/01/13 17:29:31 (GMT +200)                                           
  lib/ansible/modules/core: (detached HEAD b766390ae2) last updated 2014/12/05 14:16:32 (GMT +200)                   
  lib/ansible/modules/extras: (detached HEAD 19e688b017) last updated 2014/12/05 14:16:37 (GMT +200)                 
  v2/ansible/modules/core: (detached HEAD cb69744bce) last updated 2014/12/05 14:16:41 (GMT +200)                    
  v2/ansible/modules/extras: (detached HEAD 8a4f07eecd) last updated 2014/12/05 14:16:46 (GMT +200)                  
  configured module search path = None  
```

python and boto version

```
python
Python 2.7.5 (default, Nov  3 2014, 14:33:39) 
[GCC 4.8.3 20140911 (Red Hat 4.8.3-7)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import boto
>>> boto.Version
'2.34.0'
>>> 
```

#Running it

Variables are found under:
group_vars/all/vars.yml

Make sure you put in your AWS Credentials, and modify the other variables according to your needs.
The 'office-ip' variable is supposed to take your public IP address.

I am using a Debian 7.8 Wheezy AMI.

You can see the AMIs corresponding to your region here: https://wiki.debian.org/Cloud/AmazonEC2Image/Wheezy 

Here is the first run:

```
[dvaida@scat ansible-squid3]$ ansible-playbook vpc.yml 

PLAY [localhost] ************************************************************** 

TASK: [creating the VPC] ****************************************************** 
changed: [localhost -> 127.0.0.1]

PLAY [localhost] ************************************************************** 

TASK: [creating the office-ssh security group] ******************************** 
changed: [localhost -> 127.0.0.1]

TASK: [creating the squid3 security group] ************************************ 
changed: [localhost -> 127.0.0.1]

PLAY [localhost] ************************************************************** 

TASK: [whoami] **************************************************************** 
changed: [localhost]

TASK: [generating a new SSH key for the current user] ************************* 
ok: [localhost]

TASK: [creating the EC2 keypair] ********************************************** 
ok: [localhost -> 127.0.0.1] => (item=ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDI7kb2348qm40+sKRdQ76RTZIgdgtDQlW0FWmseecCCLUqpB8ocnI/BkCnluHgBReU6L4RQqY83n7HxZublcaH+D84t1Avy6fOag5kIij2uFzg/A2pngQWfRpYbw7riLTF7w6OrJ7Ruab7TCo+C60yQ5d5dozV+QZ8YmtQIyWiqmxnZVEIsAIPDBhUna9EK5fmaTbOZl0IGAm4zgW1lbwNtdGmBNfOh1lbgj1UZ3D+HTLfF3vbqofP75l4+kP+iOEd4/J8XioSBitkxUBM+DIQ+5PCDda7J3k68oNsUJT8dXnmC400w/4Pphapod+Ht0vQlClWAz0f0hUpCiE/Kb3T ansible-generated)

TASK: [spinning up the Squid3 instance] *************************************** 
changed: [localhost -> 127.0.0.1]

TASK: [adding EIP to the instance] ******************************************** 
changed: [localhost -> 127.0.0.1] => (item={'ramdisk': None, 'kernel': None, 'root_device_type': 'ebs', 'private_dns_name': 'ip-10-0-0-246.eu-central-1.compute.internal', 'key_name': 'squid3_key', 'public_ip': None, 'image_id': 'ami-86a9999b', 'private_ip': '10.0.0.246', 'public_dns_name': u'', 'state_code': 16, 'id': 'i-cbe69c05', 'placement': 'eu-central-1a', 'ami_launch_index': '0', 'dns_name': u'', 'region': 'eu-central-1', 'ebs_optimized': False, 'launch_time': '2015-01-28T14:46:52.000Z', 'instance_type': 't2.micro', 'state': 'running', 'root_device_name': '/dev/xvda', 'hypervisor': 'xen', 'virtualization_type': 'hvm', 'architecture': 'x86_64'})

TASK: [adding instance to host group] ***************************************** 
ok: [localhost -> 127.0.0.1] => (item={'public_ip': '54.93.90.117', 'item': {'ramdisk': None, 'kernel': None, 'root_device_type': 'ebs', 'private_dns_name': 'ip-10-0-0-246.eu-central-1.compute.internal', 'key_name': 'squid3_key', 'public_ip': None, 'image_id': 'ami-86a9999b', 'private_ip': '10.0.0.246', 'public_dns_name': u'', 'state_code': 16, 'id': 'i-cbe69c05', 'placement': 'eu-central-1a', 'ami_launch_index': '0', 'dns_name': u'', 'region': 'eu-central-1', 'ebs_optimized': False, 'launch_time': '2015-01-28T14:46:52.000Z', 'instance_type': 't2.micro', 'state': 'running', 'architecture': 'x86_64', 'hypervisor': 'xen', 'virtualization_type': 'hvm', 'root_device_name': '/dev/xvda'}, 'changed': True, 'invocation': {'module_name': u'ec2_eip', 'module_args': ''}})

TASK: [tagging instance] ****************************************************** 
changed: [localhost -> 127.0.0.1] => (item={'ramdisk': None, 'kernel': None, 'root_device_type': 'ebs', 'private_dns_name': 'ip-10-0-0-246.eu-central-1.compute.internal', 'key_name': 'squid3_key', 'public_ip': None, 'image_id': 'ami-86a9999b', 'private_ip': '10.0.0.246', 'public_dns_name': u'', 'state_code': 16, 'id': 'i-cbe69c05', 'placement': 'eu-central-1a', 'ami_launch_index': '0', 'dns_name': u'', 'region': 'eu-central-1', 'ebs_optimized': False, 'launch_time': '2015-01-28T14:46:52.000Z', 'instance_type': 't2.micro', 'state': 'running', 'root_device_name': '/dev/xvda', 'hypervisor': 'xen', 'virtualization_type': 'hvm', 'architecture': 'x86_64'})

TASK: [adding instances to local host group] ********************************** 
ok: [localhost -> 127.0.0.1] => (item={'public_ip': '54.93.90.117', 'item': {'ramdisk': None, 'kernel': None, 'root_device_type': 'ebs', 'private_dns_name': 'ip-10-0-0-246.eu-central-1.compute.internal', 'key_name': 'squid3_key', 'public_ip': None, 'image_id': 'ami-86a9999b', 'private_ip': '10.0.0.246', 'public_dns_name': u'', 'state_code': 16, 'id': 'i-cbe69c05', 'placement': 'eu-central-1a', 'ami_launch_index': '0', 'dns_name': u'', 'region': 'eu-central-1', 'ebs_optimized': False, 'launch_time': '2015-01-28T14:46:52.000Z', 'instance_type': 't2.micro', 'state': 'running', 'architecture': 'x86_64', 'hypervisor': 'xen', 'virtualization_type': 'hvm', 'root_device_name': '/dev/xvda'}, 'changed': True, 'invocation': {'module_name': u'ec2_eip', 'module_args': ''}})

TASK: [waiting for the instances to start] ************************************ 
ok: [localhost -> 127.0.0.1] => (item={'public_ip': '54.93.90.117', 'item': {'ramdisk': None, 'kernel': None, 'root_device_type': 'ebs', 'private_dns_name': 'ip-10-0-0-246.eu-central-1.compute.internal', 'key_name': 'squid3_key', 'public_ip': None, 'image_id': 'ami-86a9999b', 'private_ip': '10.0.0.246', 'public_dns_name': u'', 'state_code': 16, 'id': 'i-cbe69c05', 'placement': 'eu-central-1a', 'ami_launch_index': '0', 'dns_name': u'', 'region': 'eu-central-1', 'ebs_optimized': False, 'launch_time': '2015-01-28T14:46:52.000Z', 'instance_type': 't2.micro', 'state': 'running', 'architecture': 'x86_64', 'hypervisor': 'xen', 'virtualization_type': 'hvm', 'root_device_name': '/dev/xvda'}, 'changed': True, 'invocation': {'module_name': u'ec2_eip', 'module_args': ''}})

PLAY [squid:squid_host] ******************************************************* 

GATHERING FACTS *************************************************************** 
ok: [54.93.90.117]

TASK: [squid | updating APT cache and installing squid3] ********************** 
changed: [54.93.90.117]

TASK: [squid | setting up the squid3 configuration file] ********************** 
changed: [54.93.90.117]

NOTIFIED: [squid | restart squid3] ******************************************** 
changed: [54.93.90.117]

PLAY RECAP ******************************************************************** 
54.93.90.117               : ok=4    changed=3    unreachable=0    failed=0   
localhost                  : ok=12   changed=7    unreachable=0    failed=0
```

Subsequent runs of squid.yml (because for example, you already have the instance ready):

```
[dvaida@scat ansible-squid3]$ ansible-playbook squid.yml 

PLAY [squid:squid_host] ******************************************************* 

GATHERING FACTS *************************************************************** 
ok: [54.93.90.117]

TASK: [squid | updating APT cache and installing squid3] ********************** 
ok: [54.93.90.117]

TASK: [squid | setting up the squid3 configuration file] ********************** 
ok: [54.93.90.117]

PLAY RECAP ******************************************************************** 
54.93.90.117               : ok=3    changed=0    unreachable=0    failed=0
```
