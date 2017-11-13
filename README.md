# OpenShift-Ansible

Installation and Verification

There are two primary methods of installation for OpenShift Container Platform.

    Quick Installation The quick installation provides a simplified experience through an interactive CLI. It does not support as much customization or configuration of the OpenShift environment. For quick and simple POC-type scenarios, it can be a useful tool.

    Advanced Installation You will use the advanced installation method in this exercise. The advanced installation method involves configuring an Ansible hosts file and then invoking Ansible directly to run the installation playbooks. The advanced installer supports many, many configuration and customization options.

We have pre-configured the installer’s configuration file (/etc/ansible/hosts) for you.

For more information on installing OpenShift Container Platform, please refer to the installation section of the product documentation.



Examining the provided Ansible inventory file

First, let’s examine the provided installer configuration file.

Look at the file

Use cat, less, or an editor to look at the /etc/ansible/hosts file:

cat /etc/ansible/hosts

General settings and other variable information is defined on lines within the [OSEv3:vars] section. There are also various host groups defined for things like Masters and Nodes.


Installing OpenShift

Since we have pre-configured the installer for you, you can simply run it.

Run the Installer

Launching the advanced installation is as simple as executing the following command:

ansible-playbook -i /etc/ansible/hosts /usr/share/ansible/openshift-ansible/playbooks/byo/config.yml



s will take some time. You might want to go get a beverage or check your email. Come back in about 7-10 minutes. We have pre-cached as much of the content as possible to make the installation go quickly.
	

The additional command-line switch -i /etc/ansible/hosts in the command above is actually redundant. It directs Ansible to use an inventory file at the supplied path. When omitted, Ansible will look at a number of default paths, among which is /etc/ansible/hosts.

A typical multi-host installation like this might take around an hour depending on the speed of your internet connection. Disconnected installation options are also available. Prerequisites and other information is all covered in the documentation.
Verifying the Installation

Once the installation process is complete, there are some basic tests you can perform to ensure that it went successfully.
Check the Logs

The installer is quite verbose, and can be made even more verbose. If you know anything about Ansible, the output should be familiar to you. In any case, so long as there are no fatal-type errors and all hosts exit "OK", the odds are good that the installation was successful.

If you look in your terminal at the output of the installer, it should finish with something that looks like the following:

PLAY RECAP ***********************************************************************************
infra.internal.aws.testdrive.openshift.com  : ok=241  changed=53   unreachable=0    failed=0
localhost                                   : ok=11   changed=0    unreachable=0    failed=0
master.internal.aws.testdrive.openshift.com : ok=703  changed=157  unreachable=0    failed=0
node01.internal.aws.testdrive.openshift.com : ok=238  changed=51   unreachable=0    failed=0
node02.internal.aws.testdrive.openshift.com : ok=238  changed=51   unreachable=0    failed=0
node03.internal.aws.testdrive.openshift.com : ok=238  changed=51   unreachable=0    failed=0

You will notice zero failed tasks.
Look at the Nodes

As an administrator, most of your interaction with OpenShift will be from the command line. The oc program is a command line interface that talks to the OpenShift API.

On the master host, after the installation, the root system account is preconfigured to use a special "super administrator" account. It is vitally important that you protect access to the root system account, or to remove this preconfigured config. Otherwise, anyone who can sudo on the master has super user privileges on the entire cluster.

Additionally, since we ran the installation as cloud-user, this account, too, is preconfigured for cluster administrator access.

Execute the following command to see a list of the Nodes that OpenShift knows about:

oc get nodes

The output should look something like the following:

NAME                          STATUS                     AGE
infra.internal.aws.testdrive.openshift.com     Ready                      1m
master.internal.aws.testdrive.openshift.com    Ready,SchedulingDisabled   1m
node01.internal.aws.testdrive.openshift.com    Ready                      1m
node02.internal.aws.testdrive.openshift.com    Ready                      1m
node03.internal.aws.testdrive.openshift.com    Ready                      1m

All of the systems listed in the [nodes] group in the /etc/ansible/hosts file should be listed here.

The OpenShift Master is also a Node because it needs to participate in the software defined network (SDN). By default, however, scheduling is disabled — no workload will be run on masters.
Check the Web Console

OpenShift provides a web console for users, developers and application operators to interact with the environment. There are not many cluster administrative functions to perform through the web console. Some OpenShift components (like the internal image registry) run as workload inside the OpenShift environment, and you could see these things. However, we have not yet explored authentication topics, so you have no cluster administrator "human" accounts yet.

Point your browser to https://openshift.647073518612.aws.testdrive.openshift.com/console to verify that the web console is available and responding. You can login using the user teamuser1 with password openshift. You are not required to do anything in the web console at this point.
	You will receive a self-signed certificate error in your browser. When OpenShift is installed, by default, a CA and SSL certificates are generated for all inter-component communication within OpenShift, including the web console. It is possible to provide your own SSL certificates during the installation, and more information can be found in the custom certificates section of the installation documentation. 


