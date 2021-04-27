Ansible - Configuration Management

Ansible is simple open source IT engine which automates application deployment, intra
service orchestration, cloud provisioning and many other IT tools.

What is Configuration Management
Configuration management in terms of Ansible means that it maintains configuration of
the product performance by keeping a record and updating detailed information which
describes an enterprise’s hardware and software.

How ansible works

Ansible works by connecting to your nodes and pushing out small programs, called
"Ansible modules" to them. Ansible then executes these modules (over SSH by default),
and removes them when finished. Your library of modules can reside on any machine, and
there are no servers, daemons, or databases required.

Mainly, there are two types of machines when we talk about deployment:
Control machine: Machine from where we can manage other machines.
Remote machine: Machines which are handled/controlled by control machine.

Installation through Apt on Ubuntu Machine
For installing Ansible you have to configure PPA on your machine. For this, you have to
run the following line of code:
$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible

Every YAML file optionally starts with “---” and ends with “...”.

Key-Value pair

YAML uses simple key-value pair to represent the data. The dictionary is represented in
key: value pair.
Note: There should be space between : and value.
Example: A student record
--- #Optional YAML start syntax
james:
 name: james john
 rollNo: 34
 div: B
 sex: male
… #Optional YAML end syntax
Abbreviation
You can also use abbreviation to represent dictionaries.
Example
James: {name: james john, rollNo: 34, div: B, sex: male}

Representing List
We can also represent List in YAML. Every element(member) of list should be written in a
new line with same indentation starting with “- “ (- and space).

---
countries:
 - America
 - China
 - Canada
 - Iceland
…

Abbreviation
You can also use abbreviation to represent lists.
Example
Countries: [‘America’, ‘China’, ‘Canada’, ‘Iceland’]

List of Dictionaries
We can also make list of dictionaries.
Example
---
- james:
 name: james john
 rollNo: 34
Ansible
7
 div: B
 sex: male
 likes:
 - maths
 - physics
 - english
- robert:
 name: robert richardson
 rollNo: 53
 div: B
 sex: male
 likes:
 - biology
 - chemistry
… 


YAML uses “|” to include newlines while showing multiple lines and “>” to suppress
newlines while showing multiple lines. Due to this we can read and edit large lines. In both
the cases intendentation will be ignored.


passed: TRUE
messageIncludeNewLines: |
 Congratulation!!
 You passed with 79%
messageExcludeNewLines: >
 Congratulation!!
 You passed with 79%

Some common words related to Ansible.
Service/Server – A process on the machine that provides the service
Machine – A physical server , vm(virtual machine) or a container
Target machine – A machine we are about to configure with Ansible
Task – An action(run this, delete that) etc managed by Ansible.
Playbook – the yml file where Ansible commands are written and yml is executed
on a machine.


Parallelism and Shell Commands
Reboot your company server in 12 parallel forks at time. For this, we need to set up SSHagent for connection.
$ ssh-agent bash
$ ssh-add ~/.ssh/id_rsa
To run reboot for all your company servers in a group, 'abc', in 12 parallel forks:
$ Ansible abc -a "/sbin/reboot" -f 12
By default, Ansible will run the above Ad-hoc commands form current user account. If you
want to change this behavior, you will have to pass the username in Ad-hoc commands as
follows:
$ Ansible abc -a "/sbin/reboot" -f 12 -u username


Playbooks are the files where Ansible code is written. Playbooks are written in YAML
format. YAML stands for Yet Another Markup Language. Playbooks are one of the core
features of Ansible and tell Ansible what to execute. They are like a to-do list for Ansible
that contains a list of tasks.

Ansible - Playbook
---
 name: install and configure DB
 hosts: testServer
 become: yes
 vars:
oracle_db_port_value : 1521

 tasks:
 -name: Install the Oracle DB
 yum: <code to install the DB>

 -name: Ensure the installed service is enabled and running
5. Ansible – Playbooks
Ansible
12
 service:
 name: <your service name>
Save it as DB.yaml

ansible-playbook DB.yaml

name
This tag specifies the name of the Ansible playbook. As in what this playbook will be doing.
Any logical name can be given to the playbook.

hosts
This tag specifies the lists of hosts or host group against which we want to run the task.
The hosts field/tag is mandatory. It tells Ansible on which hosts to run the listed tasks. The
tasks can be run on the same machine or on a remote machine. One can run the tasks on
multiple machines and hence hosts tag can have a group of hosts’ entry as well.

vars
Vars tag lets you define the variables which you can use in your playbook. Usage is similar
to variables in any programming language.

tasks
All playbooks should contain tasks or a list of tasks to be executed. Tasks are a list of
actions one needs to perform. A tasks field contains the name of the task. This works as
the help text for the user. It is not mandatory but proves useful in debugging the playbook.
Each task internally links to a piece of code called a module. A module that should be
executed, and arguments that are required for the module you want to execute.

Ansible - Roles

Roles provide a framework for fully independent, or interdependent collections of
variables, tasks, files, templates, and modules.
In Ansible, the role is the primary mechanism for breaking a playbook into multiple files.
This simplifies writing complex playbooks, and it makes them easier to reuse. The
breaking of playbook allows you to logically break the playbook into reusable components.

Roles are not playbooks. Roles are small functionality which can be independently used
but have to be used within playbooks. There is no way to directly execute a role. Roles
have no explicit setting for which host the role will apply to. 

Creating New Role
ansible-galaxy -h

$ ansible-galaxy init vivekrole
ERROR! The API server (https://galaxy.ansible.com/api/) is not responding,
please try again later.
$ ansible-galaxy init --force --offline vivekrole
- vivekrole was created successfully
$ tree vivekrole/


Ansible variables

Variable in playbooks are very similar to using variables in any programming language. 
Example
- hosts : <your hosts>
 vars:
 tomcat_port : 8080


Exception Handling in Playbooks
Exception handling in Ansible is similar to exception handling in any programming
language. An example of the exception handling in playbook is shown below.
tasks:
 - name: Name of the task to be executed
 block:
 - debug: msg='Just a debug message , relevant for logging'
 - command: <the command to execute>
 rescue:
 - debug: msg='There was an exception.. '
 - command: <Rescue mechanism for the above exception occurred)
 always:
 - debug: msg="this will execute in all scenarios. Always will get
logged"

- {role: start-tomcat, tags: ['install']}}
The following command helps in using tags:
ansible-playbook -i hosts <your yaml> --tags "install" -vvv
With the above command, only the start-tomcat role will be called. The tag provided is
case-sensitive. Ensure exact match is being passed to the command.


ssh-copy-id user@ip
#From control machine to remote server copy public key to node server to ~/.authorized_keys
#Which will help to login to server password less and perform operation

Ansible keeps track of its nodes using an inventory file.

ansible all -m ping
ansible -a "/bin/hostnamectl --static" agentnode
ansible nginx -u root -m ping

for server in serverA serverB serverC; do ssh ${server} "useradd myuser"; done
This would create myuser on serverA, serverB, and serverC; however, it would run the user add command every
single time the for-loop was run, whether or not the user existed. An idempotent system will first check whether
the user exists, and if it does not, the tool will create it. This is a simplified example,
of course, but the benefits of an idempotent tool will become more clear over time.

hosts:
var:
tasks:
Finally the main section is indicated by tasks:. This is where most of the instructions are located.
Each task should have a - name:. This is what is displayed as Ansible is carrying out a run, or playbook execution.

command - Executes a command on a remote node
script - Runs a local script on a remote node after transferring it
shell - Execute commands in nodes
mysql_db - Add or remove MySQL databases from a remote host
mysql_user - Adds or removes a user from a MySQL database
postgresql_db - Add or remove PostgreSQL databases from a remote host
postgresql_user - Adds or removes a users (roles) from a PostgreSQL database
fetch - Fetches a file from remote nodes
template - Templates a file out to a remote server
yum - Manages packages with the yum package manager
apt - Manages apt-packages
git - Deploy software (or files) from git checkouts
service - Manage services

#Run remotely 
 ansible-playbook connectserver.yml  -i hosts(is file with inventroy mentioned init)

ssh-copy-id -i ~/.ssh/id_rsa.pub user@IP
https://docs.ansible.com/ansible/latest/user_guide/basic_concepts.html

modules
System
Commands
Files
Database
Cloud
Windows

You can also define aliases in your inventory:

In INI:

jumper ansible_port=5555 ansible_host=192.0.2.50
In YAML:

...
  hosts:
    jumper:
      ansible_port: 5555
      ansible_host: 192.0.2.50

Assigning a variable to many machines: group variables
If all hosts in a group share a variable value, you can apply that variable to an entire group at once. In INI:

[atlanta]
host1
host2

[atlanta:vars]
ntp_server=ntp.atlanta.example.com
proxy=proxy.atlanta.example.com
In YAML:

atlanta:
  hosts:
    host1:
    host2:
  vars:
    ntp_server: ntp.atlanta.example.com
    proxy: proxy.atlanta.example.com

Patterns: targeting hosts and groups

For example:

ansible webservers -m service -a "name=httpd state=restarted"
In a playbook the pattern is the content of the hosts: line for each play:

- name: <play_name>
ansible agentnode -m setup -u root -i hosts
127.0.0.1 -B 1 -P 0 -a "/usr/bin/ls --do-stuff"
B 1 timeout for 1 second

ansible-playbook conditional.yml --check #Dry run no operation
check_mode: yes


rc
Some modules execute command line utilities or are geared for executing commands directly (raw, shell, command, etc),
this field contains ‘return code’ of these utilities.

results
