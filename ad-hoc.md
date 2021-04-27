ad-hoc commands are helpul to run the commands on the fly
Not useful like writing ansible-playbook

Sytnax
$ ansible [pattern] -m [module] -a "[module options]"

$ ansible atlanta -a "/sbin/reboot"
By default Ansible uses only 5 simultaneous processes. If you have more hosts than the value set for the fork count,
Ansible will talk to them, but it will take a little longer. To reboot the [atlanta] servers with 10 parallel forks:

$ ansible atlanta -a "/sbin/reboot" -f 10

$ ansible atlanta -a "/sbin/reboot" -f 10 -u username
Rebooting probably requires privilege escalation. You can connect to the server as username and run
the command as the root user by using the become keyword:

$ ansible atlanta -a "/sbin/reboot" -f 10 -u username --become [--ask-become-pass]

$ ansible atlanta -m copy -a "src=/etc/hosts dest=/tmp/hosts"
If you plan to repeat a task like this, use the template module in a playbook.

The file module allows changing ownership and permissions on files.
These same options can be passed directly to the copy module as well:

$ ansible webservers -m file -a "dest=/srv/foo/a.txt mode=600"
$ ansible webservers -m file -a "dest=/srv/foo/b.txt mode=600 owner=mdehaan group=mdehaan"

The file module can also create directories, similar to mkdir -p:

$ ansible webservers -m file -a "dest=/path/to/c mode=755 owner=mdehaan group=mdehaan state=directory"
As well as delete directories (recursively) and delete files:

$ ansible webservers -m file -a "dest=/path/to/c state=absent"

Managing Services
Ensure a service is started on all webservers:

$ ansible webservers -m service -a "name=httpd state=started"
