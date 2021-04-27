Working With Playbooks
======================
https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html

Playbooks are Ansible’s configuration, deployment, and orchestration language.
They can describe a policy you want your remote systems to enforce, or a set of steps in a general IT process.

If Ansible modules are the tools in your workshop, playbooks are your instruction manuals,
and your inventory of hosts are your raw material.

The goal of a play is to map a group of hosts to some well defined roles, represented by things ansible calls tasks.
At a basic level, a task is nothing more than a call to an ansible module.

Tasks list
Each play contains a list of tasks. Tasks are executed in order, one at a time,
against all machines matched by the host pattern, before moving on to the next task.
It is important to understand that, within a play, all hosts are going to get the
same task directives. It is the purpose of a play to map a selection of hosts to tasks.

The command and shell modules are the only modules that just take a list of arguments and don’t use the key=value form.
This makes them work as simply as you would expect:

tasks:
  - name: enable selinux
    command: /sbin/setenforce 1

Handlers are lists of tasks, not really any different from regular tasks, that are referenced by a globally unique name,
and are notified by notifiers. If nothing notifies a handler, it will not run. Regardless of how many tasks notify a handler,
it will run only once, after all of the tasks complete in a particular play.

Here’s an example handlers section:

handlers:
    - name: restart memcached
      service:
        name: memcached
        state: restarted
    - name: restart apache
      service:
        name: apache
        state: restarted

For example, if you run ansible-lint on the nginx.yml playbook introduced earlier in this section, you’ll get the following results:

$ ansible-lint nginx.yml

Creating Reusable Playbooks
 - Including and Importing
 - Roles

 If you use any include* Task (include_tasks, include_role, etc.), it will be dynamic.
 If you use any import* Task (import_playbook, import_tasks, etc.), it will be static.

All import* statements are pre-processed at the time playbooks are parsed.
All include* statements are processed as they are encountered during the execution of the playbook.

- import_playbook: connectserver.yml
or 
- include_playbook: connectserver.yml
You can write the common activies in single file and import/include in other yaml so that we can avoid repeating

Roles are ways of automatically loading certain vars_files, tasks, and handlers based on a known file structure.
Grouping content by roles also allows easy sharing of roles with other users.

Role Directory Structure
Example project structure:

site.yml
webservers.yml
fooservers.yml
roles/
    common/
        tasks/
        handlers/
        files/
        templates/
        vars/
        defaults/
        meta/
    webservers/
        tasks/
        defaults/
        meta/

When in use, each directory must contain a main.yml file, which contains the relevant content:

tasks - contains the main list of tasks to be executed by the role.
handlers - contains handlers, which may be used by this role or even anywhere outside this role.
defaults - default variables for the role (see Using Variables for more information).
vars - other variables for the role (see Using Variables for more information).
files - contains files which can be deployed via this role.
templates - contains templates which can be deployed via this role.
meta - defines some meta data for this role. See below for more details.

Hey wait, a YAML gotcha
YAML syntax requires that if you start a value with {{ foo }} you quote the whole line,
since it wants to be sure you aren’t trying to start a YAML dictionary. This is covered on the YAML Syntax documentation.

This won’t work:

- hosts: app_servers
  vars:
      app_path: {{ base_path }}/22

Do it like this and you’ll be fine:

- hosts: app_servers
  vars:
       app_path: "{{ base_path }}/22"
Variables discovered from s

Variables discovered from systems: Facts
ansible 172.16.129.43 -u root -m setup
name: Nginx template files to parse {{ ansible_facts }}

Disabling facts
If you know you don’t need any fact data about your hosts, and know everything about your systems centrally,
you can turn off fact gathering. This has advantages in scaling Ansible in push mode with very large numbers of systems,
mainly, or if you are using Ansible on experimental platforms. In any play, just do this:

- hosts: whatever
  gather_facts: no

For example, assume /etc/ansible/facts.d/preferences.fact contains:

[general]
asdf=1
bar=2
This will produce a hash variable fact named general with asdf and bar as members. To validate this, run the following:

ansible <hostname> -m setup -a "filter=ansible_local"
And you will see the following fact added:

"ansible_local": {
        "preferences": {
            "general": {
                "asdf" : "1",
                "bar"  : "2"
            }
        }
 }
And this data can be accessed in a template/playbook as:

{{ ansible_local['preferences']['general']['asdf'] }}

Accessing information about other hosts with magic variables
The most commonly used magic variables are hostvars, groups, group_names, and inventory_hostname.

A frequently used idiom is walking a group to find all IP addresses in that group.

{% for host in groups['app_servers'] %}
   {{ hostvars[host]['ansible_facts']['eth0']['ipv4']['address'] }}
{% endfor %}

Remember: Child groups override parent groups, and hosts always override their groups.
